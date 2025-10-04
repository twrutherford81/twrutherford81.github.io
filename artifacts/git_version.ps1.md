```powershell
# Generates version info for a project from git informaton.
#
# NB: this script was originally written for use with another project
# and has been adapted for use here.
#
# Author: Thomas Rutherford
# Version: 1.0

# Command line parameters
param (
    [string]$Directory,
    [string]$Filename,
    [switch]$Header,
    [switch]$Text
)

$SCRIPT_NAME = $MyInvocation.MyCommand.Name

function Show-Usage {
    Write-Output ""
    Write-Output "Usage: $SCRIPT_NAME [-directory | -filename | -header | -text]"
    Write-Output ""
    Write-Output "    -d -directory    Specifies the directory to place the generated file in"
    Write-Output "    -f -filename     Specifies the name of the file"
    Write-Output "    -h -header       Specifies to generate C-style header output"
    Write-Output "    -t -text         Specifies to generate human-readable output"
}

if (-not $Directory -or -not $Filename -or (-not $Header -and -not $Text)) {
    Show-Usage
    exit 255
}

$currentYear = (Get-Date).Year
$csetInfo = git describe --long

# 'git describe --long' reports a summary of the changeset in the following format:
#	X.X.X.X-Y-gZZZZZZZ
#
# For development builds, the command reports a summary of the changeset in the following
# format:
#
# dev/X.X.X.X-Y-gZZZZZZZ
#
# Where:
#	1.) X.X.X.X = The tag format <Major.Minor.Hotfix.Patch>
#	2.) Y = Number of changesets since the last reachable tag
#	3.) Z = Changeset short ID, prefixed by 'g' to denote the use of Git as the SCM
#
# Based on the format reported by 'git describe', this script can determine the
# appropriate versioning information to apply.  For development builds, we chop off
# the 'dev/' from the reported information to get at the real information we want.

if ($csetInfo -like "dev*") {
    $csetInfo = $csetInfo -replace "^dev/", ""
}

$tag = $csetInfo.Split('-')[0]
$patchDist = $csetInfo.Split('-')[1]
$csetHash = $csetInfo.Split('-')[2] -replace "^g", ""

# Parse the tag into the relevant versioning numbers.
$majorRev = $tag.Split('.')[0]
$minorRev = $tag.Split('.')[1]
$hotfxRev = $tag.Split('.')[2]
$patchRev = [int]$patchDist + [int]$tag.Split('.')[3]

# Preserve the old header to see if anything changed
$oldHeader = Join-Path -Path $Directory -ChildPath "$Filename.old"
$newHeader = Join-Path -Path $Directory -ChildPath $Filename

if (Test-Path $newHeader) {
    Move-Item -Force -Path $newHeader -Destination $oldHeader
}

# Create the new file.
if ($Header) {
    @"
#ifndef __git_version_h__
#define __git_version_h__

#define GIT_TAG_MAJOR_REV    $([int]$majorRev)
#define GIT_TAG_MINOR_REV    $([int]$minorRev)
#define GIT_TAG_HOTFX_REV    $([int]$hotfxRev)
#define GIT_TAG_PATCH_REV    $([int]$patchRev)
#define GIT_TAG_STRING       $majorRev.$minorRev.$hotfxRev.$patchRev
#define GIT_TAG_DISTANCE     $patchDist
#define GIT_TAG_CHANGESET    $csetHash

/* For maintaining the fully copyright string */
#define CURRENT_YEAR    "$currentYear"

#endif /* __git_version_h__ */
"@ | Out-File -FilePath $newHeader -Encoding ascii
}

if ($Text) {
    @"
GIT_TAG_MAJOR_REV=$([int]$majorRev)
GIT_TAG_MINOR_REV=$([int]$minorRev)
GIT_TAG_HOTFX_REV=$([int]$hotfxRev)
GIT_TAG_PATCH_REV=$([int]$patchRev)
GIT_TAG_STRING=$majorRev.$minorRev.$hotfxRev.$patchRev
GIT_TAG_DISTANCE=$patchDist
GIT_TAG_CHANGESET=$csetHash
CURRENT_YEAR=$currentYear
"@ | Out-File -FilePath $newHeader -Encoding ascii
}

if (Test-Path $oldHeader) {
    # 0 -> no differences, 1 -> differences, 2 -> error
    if (Compare-Object `
            -ReferenceObject (Get-Content $oldHeader) `
            -DifferenceObject (Get-Content $newHeader) `
            -SyncWindow 0) {
        Copy-Item -Force -Path $oldHeader -Destination $newHeader
    }
    Remove-Item -Force -Path $oldHeader
}

exit 0
```