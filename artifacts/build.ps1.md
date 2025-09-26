---
layout: default
title: Thomas Rutherford
description: build.ps1
back_link: ../index.html
---

```powershell
# A powershell script to build a .NET application.
#
# NB: this script was originally written for use with another project
# and has been adapted for use here.
#
# Author: Thomas Rutherford
# version: 1.0

# Command line params
param (
    [switch]$Build,
    [switch]$Clean,
    [switch]$Version
)

$SCRIPT_NAME = $MyInvocation.MyCommand.Name

# Build variables
$BUILD_DIR = "build/"
$BUILD_ENV_LOG = "buildenv.log"
$CHECKSUM_FILE = "md5checksums.txt"
$VERSION_TEXT_FILE = "version.txt"
$VERSION_SCRIPT = "git_version.ps1"

# Projects and directories
$DOT_NET_PROJECT_DIR = "StatesSearch"
$DOT_NET_PROJECT_FILE = "$DOT_NET_PROJECT_DIR/StatesSearch.csproj"
$DOT_NET_OUTPUT_DIR = "" # To be determined at build time
$MSI_PROJECT_DIR = "WixSetup"
$MSI_PROJECT_FILE = "$MSI_PROJECT_DIR/WixSetup.wixproj"

# Configurations and platforms to be built
$CONFIGURATIONS = @("Release")
$PLATFORMS = @("x86", "x64")

# Version placeholders
$GIT_TAG_MAJOR_REV = ""
$GIT_TAG_MINOR_REV = ""
$GIT_TAG_HOTFX_REV = ""
$GIT_TAG_PATCH_REV = ""
$GIT_TAG_STRING = ""
$GIT_TAG_DISTANCE = ""

## Prints the script usage info
function Show-Usage {
    Write-Output ""
    Write-Output "Usage: $SCRIPT_NAME [-build | -clean | -version ]"
    Write-Output ""
    Write-Output "    -b -build      Build the EXE/MSI"
    Write-Output "    -c -clean      Clean the build directories and build cache"
    Write-Output "    -v -version    Generate the version information for Bamboo"
}

## Reads the version.txt into script variables
function Read-Version-Info {
    Get-Content $VERSION_TEXT_FILE | ForEach-Object {
        $line = $_
        $var_name, $var_value = $line -split '='
        switch ($var_name) {
            # Specify the script scope otherwise local varialbes are created
            "GIT_TAG_MAJOR_REV" { $script:GIT_TAG_MAJOR_REV = $var_value }
            "GIT_TAG_MINOR_REV" { $script:GIT_TAG_MINOR_REV = $var_value }
            "GIT_TAG_HOTFX_REV" { $script:GIT_TAG_HOTFX_REV = $var_value }
            "GIT_TAG_PATCH_REV" { $script:GIT_TAG_PATCH_REV = $var_value }
            "GIT_TAG_STRING" { $script:GIT_TAG_STRING = $var_value }
            "GIT_TAG_DISTANCE" { $script:GIT_TAG_DISTANCE = $var_value }
        }
    }
}

# If no params are entered, print usage and end
if (-not $Build -and -not $Clean -and -not $Versn) {
    Show-Usage
    exit 255
}

# Clean the project, process before building
if ($Clean) {
    Write-Output "Cleaning build directories and build cache..."

    foreach ($configuration in $CONFIGURATIONS) {
        dotnet clean --configuration $configuration
    }

    if( Test-Path "$DOT_NET_PROJECT_DIR/bin/" ) { Remove-Item -Recurse -Force "$DOT_NET_PROJECT_DIR/bin/" }
    if( Test-Path "$DOT_NET_PROJECT_DIR/obj/" ) { Remove-Item -Recurse -Force "$DOT_NET_PROJECT_DIR/obj/" }
    if( Test-Path "$MSI_PROJECT_DIR/bin/" ) { Remove-Item -Recurse -Force "$MSI_PROJECT_DIR/bin/" }
    if( Test-Path "$MSI_PROJECT_DIR/obj/" ) { Remove-Item -Recurse -Force "$MSI_PROJECT_DIR/obj/" }
    if( Test-Path $BUILD_DIR ) { Remove-Item -Recurse -Force $BUILD_DIR }
    if( Test-Path $VERSION_TEXT_FILE ) { Remove-Item -Force $VERSION_TEXT_FILE }
    if( Test-Path $BUILD_ENV_LOG ) { Remove-Item -Force $BUILD_ENV_LOG }
    Write-Output "Done`n"
}

# Generate version.txt using the git_version script
if ($Version) {
    # Generate build version information from git
    Write-Output "Generating version information from git ..."
    & ".\$VERSION_SCRIPT" -D . -F $VERSION_TEXT_FILE -T
    Write-Output "Done`n"
}

# Build the project
if ($Build) {
    # Generate build environment log
    Write-Output "Generating build environment log..."
    Get-ChildItem Env: | Out-File -Append -FilePath $BUILD_ENV_LOG

    # Read the version info from the generated version file
    Write-Output "Reading version info..."
    Read-Version-Info
    Write-Output "Building version: $GIT_TAG_STRING"
    Write-Output "Done`n"

    # Create the build/ directory if it doesn't exist
    if (-not (Test-Path $BUILD_DIR)) {
        New-Item -ItemType Directory -Path $BUILD_DIR
    }

    # Loop through each configuration and platform to build each variant
    foreach ($configuration in $CONFIGURATIONS) {
        foreach ($platform in $PLATFORMS) {

            # Build the MSI
            Write-Output "Building $configuration $platform"
            dotnet msbuild $MSI_PROJECT_FILE -p:Configuration=$configuration -p:Platform=$platform -p:Version=$GIT_TAG_STRING -p:AssemblyVersion=$GIT_TAG_STRING -p:FileVersion=$GIT_TAG_STRING -Restore -v:n

            if ($LASTEXITCODE -ne 0) {
                Write-Output "[ERROR]: Failed to build the $configuration $platform MSI"
                Write-Output "Please review the build logs for error condition(s)"
                exit 1
            }

            # Find the MSI
            $OUT_DIR = "$MSI_PROJECT_DIR\" + $(dotnet msbuild $MSI_PROJECT_FILE -p:Configuration=$configuration -p:Platform=$platform -Restore -v:n -getProperty:OutputPath)
            $MSI = Get-ChildItem -Path $OUT_DIR -Filter "*.msi" -Recurse

            if (-not $MSI) {
                Write-Output "[ERROR]: Failed to find the $configuration $platform MSI"
                Write-Output "[ERROR]: Please review the build logs for error condition(s)"
                exit 1
            }

            # Copy the MSI to the build directory
            Copy-Item -Path $MSI.FullName -Destination $BUILD_DIR -Force
            if ($LASTEXITCODE -ne 0) {
                Write-Output "[ERROR]: Failed to copy the $configuration $platform MSI to the build output directory"
                exit 1
            }

            Write-Output "Done`n"
        }
    }

    Write-Output "Generating checksums..."
    Set-Location $BUILD_DIR
    # Split-Path is used to get the filename from the path
    Get-ChildItem -Filter *.msi -File | Get-FileHash -Algorithm MD5 | ForEach-Object { "$($_.Hash) " + $(Split-Path -Path $_.Path -Leaf) } | Out-File -FilePath $CHECKSUM_FILE
    Write-Output "Done`n"

    # Move the version file and buildenv file into the builds directory
    Set-Location ..
    if( Test-Path $VERSION_TEXT_FILE ) { Move-Item -Force -Path $VERSION_TEXT_FILE -Destination $BUILD_DIR }
    if( Test-Path $BUILD_ENV_LOG ) { Move-Item -Force -Path $BUILD_ENV_LOG -Destination $BUILD_DIR }

    Write-Output "Build finished`n"

    # Only Running dependency check on x64 Release builds
    if(-not $CONFIGURATIONS.Contains("Release") -and $PLATFORMS.Contains("x64")) {
        exit 0
    }

    Write-Output "Setting up for dependency check..."

    # Determine the output directory of the .NET project
    $DOT_NET_OUTPUT_DIR = (dotnet msbuild $DOT_NET_PROJECT_FILE `
        -nologo `
        -t:GetTargetPath `
        -property:Configuration=Release `
        -property:Platform=x64 `
        -getProperty:OutputPath `
        -verbosity:quiet).Trim()

    $SCAN_PATH = Join-Path $DOT_NET_PROJECT_DIR $DOT_NET_OUTPUT_DIR

    if ($LASTEXITCODE -ne 0 -or -not (Test-Path $SCAN_PATH)) {
        Write-Output "[ERROR]: Failed to get the .NET project OutputPath"
        exit 1
    }

    Write-Output "Dependency check scan path: $SCAN_PATH"

    Write-Output "Running dependency check..."
    & ".\dependency_check.ps1" -ScanPath $SCAN_PATH -OutputDir $BUILD_DIR
    Write-Output "Dependency check complete."
}

exit 0
```