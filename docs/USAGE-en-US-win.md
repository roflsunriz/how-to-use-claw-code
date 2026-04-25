# How to use claw-code in Windows, the step-by-step guide for beginners

## How to open terminal

### method 1
1. Push the key: Windows + R
2. Dialogue will appear, then type `wt` or `wt.exe` in text field.
3. Press Enter. Done.

### method 2
1. Press Windows Button. Start menu will appear.
2. Click `all` to browse all installed app.
3. Search `terminal` then click it.
4. Done.

### method 3 (Administorator privileges)
1. Push the key: Windows + R
2. Dialogue will appear, then type `wt` or `wt.exe` in text field.
3. Press Ctrl + Shift + Enter.
4. UAC will appear so approve it.
5. Done.

### method 4 (Administorator privileges)
1. Press Windows Button. Start menu will appear.
2. Click `all` to browse all installed app.
3. Search `terminal` then right click on it.
4. Find details sub menu.
5. Click `Run in Administrator mode`.
6. UAC will appear. Approve it.
7. Done.

### method 5 (Administorator privileges)
1. After launched terminal in normal mode, click `∨` icon.
2. Point powershell then right click on it.
3. `Run in Administrator mode` will appear so select it.
4. UAC will appear so approve it.
5. Done.

## How to install claw-code to your system

### Install newer powershell
1. Launch terminal in cmd mode. (Click `∨` then choose `Command Prompt`)
2. Execute `winget upgrade Microsoft.Powershell --source winget` to upgrade to newest version of Powershell. 
3. Click `∨` then choose settings.
4. In `Start up` tab, choose `Powershell` in `Default Profile`. Not `Windows Powershell`. `Windows Powershell` is pre-installed version for Windows and it is older.
5. Click `+` and confirm the terminal outputs something `Powershell 7.6.1` or newer version.

### Install Git
1. Execute `winget install Git.Git --source winget`
2. Restart terminal. In typical, press `+`.

### Clone Repository
1. Move the current directory to your preffered location. In this guide, `Set-Location $env:USERPROFILE\Documents`
2. `git clone ultraworkers/claw-code`

### Install Rust to build
1. `winget install Rustlang.Rustup --source winget`
2. Restart terminal.

### Build it from source code
1. `cargo build --workspace` or `cargo build --release`. Either command takes 5-15 mininutes to finish. `--workspace` is debug build and faster building. `--release` is optimized building and faster launch executable, smaller size of file but it takes time to build.


### Set API KEY to Environment Variables (Method 1, Safer, dispose when you close terminal)
1. `$env:ANTHRPOPIC_API_KEY = "SET YOUR API KEY HERE` or `$env: ANTHROPIC_AUTH_TOKEN = "SET YOUR AUTH TOKEN"` For Anthropic Claude models.
2. `$env: OPENAI_API_KEY= "SET YOUR API KEY HERE"` and `$env: OPENAI_BASE_URL="SET YOUR API ENDPOINT HERE"` (e.g. "https://openrouter.ai/api/v1", "https://api.cerebras.ai/v1", "https://127.0.0.1:8000/v1" for local models, ollama, llama.cpp, LMStudio)
3. `.\target\debug\claw.exe --model "YOUR AI MODEL"`(e.g. "openai/gpt-5.5", "anthropic/claude-opus-4-7", "deepseek/deepseek-v4-pro", etc.)

### Set API KEY to Environment Variables (Method 2, Dangerous, For daily use)
1. 
    ```powershell
    [Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "SET YOUR API KEY HERE", "User")
    [Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "SET URL HERE", "User")
    ```
2. `.\target\debug\claw.exe --model "YOUR AI MODEL"`

### Set claw.exe to Path Environment Variables
```powershell
$newPath = "C:\Users\UserName\Documents\claw-code\rust\target\debug"
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$oldPath;$newPath", "User")
```
  
then, 
  
Move current directory to your repository (e.g. `$env:USERPROFILE\Documents\my-repository`)  
  
then,  
  
You can type `claw --model "openai/gpt-5.5"` to start coding
