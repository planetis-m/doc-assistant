# chunkvec Install Fallback

Use this only when `command -v cvstore` or `command -v cvquery` fails.

Path policy:
- Use absolute user paths only (`$HOME/.local/...`).
- Never install into a relative workspace path such as `./.local/...`.
- Never create `.local` under the current project directory.

As of March 11, 2026 (`chunkvec` latest release), release assets exist for:
- Linux `x86_64`
- macOS `arm64`
- Windows `x86_64`

Release page:
- `https://github.com/planetis-m/chunkvec/releases/latest`

## Linux x86_64

```bash
set -euo pipefail
mkdir -p "$HOME/.local/opt/chunkvec" "$HOME/.local/bin"
curl -fsSL -o "$HOME/.local/opt/chunkvec/chunkvec.tar.gz" \
  "https://github.com/planetis-m/chunkvec/releases/latest/download/chunkvec-linux-x86_64.tar.gz"
rm -rf "$HOME/.local/opt/chunkvec/current"
mkdir -p "$HOME/.local/opt/chunkvec/current"
tar -xzf "$HOME/.local/opt/chunkvec/chunkvec.tar.gz" -C "$HOME/.local/opt/chunkvec/current"
ln -sfn "$HOME/.local/opt/chunkvec/current/cvstore" "$HOME/.local/bin/cvstore"
ln -sfn "$HOME/.local/opt/chunkvec/current/cvquery" "$HOME/.local/bin/cvquery"
export PATH="$HOME/.local/bin:$PATH"
cvstore --help >/dev/null
cvquery --help >/dev/null
```

## macOS arm64

```bash
set -euo pipefail
mkdir -p "$HOME/.local/opt/chunkvec" "$HOME/.local/bin"
curl -fsSL -o "$HOME/.local/opt/chunkvec/chunkvec.tar.gz" \
  "https://github.com/planetis-m/chunkvec/releases/latest/download/chunkvec-macos-arm64.tar.gz"
rm -rf "$HOME/.local/opt/chunkvec/current"
mkdir -p "$HOME/.local/opt/chunkvec/current"
tar -xzf "$HOME/.local/opt/chunkvec/chunkvec.tar.gz" -C "$HOME/.local/opt/chunkvec/current"
ln -sfn "$HOME/.local/opt/chunkvec/current/cvstore" "$HOME/.local/bin/cvstore"
ln -sfn "$HOME/.local/opt/chunkvec/current/cvquery" "$HOME/.local/bin/cvquery"
export PATH="$HOME/.local/bin:$PATH"
cvstore --help >/dev/null
cvquery --help >/dev/null
```

## Windows x86_64 (PowerShell)

```powershell
$ErrorActionPreference = "Stop"
$dst = "$HOME\.local\opt\chunkvec"
New-Item -ItemType Directory -Force -Path $dst | Out-Null
$zip = Join-Path $env:TEMP "chunkvec.zip"
Invoke-WebRequest -Uri "https://github.com/planetis-m/chunkvec/releases/latest/download/chunkvec-windows-x86_64.zip" -OutFile $zip
Remove-Item -Recurse -Force (Join-Path $dst "current") -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Force -Path (Join-Path $dst "current") | Out-Null
Expand-Archive -Path $zip -DestinationPath (Join-Path $dst "current") -Force
$store = Get-ChildItem -Path (Join-Path $dst "current") -Recurse -Filter "cvstore.exe" | Select-Object -First 1
$storeDir = Split-Path -Parent $store.FullName
$env:Path = "$storeDir;$env:Path"
cvstore --help | Out-Null
cvquery --help | Out-Null
```

## DeepInfra API key configuration

`cvstore` and `cvquery` require an API key. After installation, present these instructions to the user before running ingest or search.

**Recommended: environment variable**
Linux/macOS: `export DEEPINFRA_API_KEY="your_api_key"`
Windows PowerShell: `$env:DEEPINFRA_API_KEY = "your_api_key"`

**Alternative: update config.json**
Create or edit `config.json` inside the directory where the real binaries live (e.g., `~/.local/opt/chunkvec/current/`) and set:
```json
{
  "api_key": "your_deepinfra_api_key"
}
```

## Notes

- Keep all extracted runtime files (`config.json`, `vector`, and platform shared libs) with the real binaries.
- Do not copy only `cvstore`/`cvquery` or `cvstore.exe`/`cvquery.exe` into another directory without bundled runtime files.
- Ensure install targets are under user home (`$HOME/.local` or `%USERPROFILE%\\.local`), not the current workspace.
- If install fails due to permission/sandbox restrictions, request escalated permission and retry.
- If platform/architecture is unsupported, stop and ask the user for manual installation steps.
