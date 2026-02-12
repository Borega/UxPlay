# Bonjour SDK Setup for Windows CI Build

## Issue
The Windows build workflow requires the Bonjour SDK, but the previous download source (nelsonjchen/mDNSResponder GitHub repository) is no longer available and returns a 404 error.

## Solution Required
To enable the Windows CI build, the repository maintainer needs to upload the Bonjour SDK installer to this repository's GitHub Releases.

### Steps to Fix:

1. **Download the Bonjour SDK**
   
   Download `bonjoursdksetup.exe` from one of these official sources:
   - Apple Developer: https://developer.apple.com/download/all/?q=Bonjour%20SDK%20for%20Windows
     (Requires free Apple Developer account)
   - Softpedia mirror: https://www.softpedia.com/get/Programming/SDK-DDK/Bonjour-SDK.shtml

2. **Verify the SHA256 Hash**
   
   Before uploading, verify the downloaded file has the correct hash:
   ```bash
   # On Windows PowerShell:
   (Get-FileHash -Path bonjoursdksetup.exe -Algorithm SHA256).Hash
   
   # On Linux/Mac:
   sha256sum bonjoursdksetup.exe
   ```
   
   Expected hash:
   ```
   F76D3E563914645F6BB28C32D9BB5FDE1BFF13AD51D54D64B3C9B3A68F5C9BD0
   ```

3. **Upload to GitHub Releases**
   
   Create a new release in this repository with the tag `bonjour-sdk`:
   
   Using GitHub CLI:
   ```bash
   gh release create bonjour-sdk bonjoursdksetup.exe \
     --title "Bonjour SDK for Windows" \
     --notes "Bonjour SDK v3.0 installer required for Windows builds"
   ```
   
   Or manually through the GitHub web interface:
   - Go to: https://github.com/Borega/UxPlay/releases/new
   - Tag: `bonjour-sdk`
   - Title: "Bonjour SDK for Windows"
   - Upload: `bonjoursdksetup.exe`
   - Publish release

4. **Re-run the Failed Workflow**
   
   Once uploaded, the workflow will automatically download from:
   ```
   https://github.com/Borega/UxPlay/releases/download/bonjour-sdk/bonjoursdksetup.exe
   ```

## How the Workflow Handles This

The workflow tries multiple download sources in order:
1. This repository's releases (primary, recommended)
2. Original nelsonjchen mirror (fallback, currently unavailable)

If all sources fail, the workflow provides clear error messages with instructions.

## Alternative Solutions

If uploading to this repository is not preferred, alternative options include:

1. **Fork nelsonjchen/mDNSResponder**: Create your own fork and add the release there
2. **Use a different hosting**: Upload to any reliable file hosting and update the workflow URLs
3. **Skip Bonjour SDK**: Modify the build to work without it (loses DNS-SD functionality)

## Security Note

The workflow validates the SHA256 hash of the downloaded file before installation to ensure file integrity and prevent tampering.
