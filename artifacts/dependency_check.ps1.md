```powershell
# A powershell script to scan a .NET application for known vulnerabilities.
#
# Author: Thomas Rutherford
# version: 1.0

param (
    [string]$ProjectName = "StatesSearch",
    [string]$ScanPath = "StatesSearch\bin\Debug\net8.0-windows",
    [string]$OutputDir = "dependency-report",
    [string]$Format = "HTML"
)

# Get secrets from user profile
$secretsId = "statessearch-7906dc4f-a0dc-44bc-8bb3-32dfec1791eb"
$secretsPath = "$env:APPDATA\Microsoft\UserSecrets\$secretsId\secrets.json"

if (Test-Path $secretsPath) {
    $secrets = Get-Content $secretsPath | ConvertFrom-Json
    $NvdApiKey = $secrets.NvdApiKey
    $OssUsername = $secrets.OssUsername
    $OssToken  = $secrets.OssToken

    Write-Host "[OK] Loaded secrets from user profile"
} else {
    Write-Error "[ERROR] Secrets file not found at $secretsPath"
    Write-Error "Please set up your user secrets as per the README instructions."
    exit 1
}

# Set Java options to reduce warnings and improve performance
$env:JAVA_OPTS = "-Dorg.apache.lucene.store.MMapDirectory.enableMemorySegments=false " + `
                  "--add-modules jdk.incubator.vector"

Write-Host ""
Write-Host "[INFO] Running OWASP Dependency-Check..."

try {
    # Exclude .exe files to avoid PE analyzer issues
    $depCheckArgs = @(
        "--project", $ProjectName,
        "--scan", $ScanPath,
        "--format", $Format,
        "--out", $OutputDir,
        "--nvdApiKey", $NvdApiKey,
        "--ossIndexUsername", $OssUsername,
        "--ossIndexPassword", $OssToken,
        "--failOnCVSS", "7",
        "--enableExperimental",
        "--exclude", "*.exe"
    )

    # Run dependency-check with arguments
    & dependency-check @depCheckArgs
}
catch {
    Write-Error "[ERROR] Dependency-Check failed: $_"
    exit 1
}
finally {
    # Clean up environment variable
    $env:JAVA_OPTS = $null
}

Write-Host ""
Write-Host "[OK] All scans complete."
```