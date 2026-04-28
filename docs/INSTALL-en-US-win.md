# How to install Claw Code on Windows

This guide explains how to install and run Claw Code on Windows.
It is written for beginners and uses Windows Terminal and PowerShell.

## Open Windows Terminal

You can use any of the following methods.

### Method 1: Open from the Run dialog

1. Press <kbd>Windows</kbd> + <kbd>R</kbd>.
2. Type `wt` or `wt.exe`.
3. Press <kbd>Enter</kbd>.

### Method 2: Open from the Start menu

1. Press the <kbd>Windows</kbd> key.
2. Open the list of all installed apps.
3. Search for `Terminal`.
4. Click **Terminal**.

??? info "Open as administorator"

      Method 3: Open as administrator from the Run dialog

      1. Press <kbd>Windows</kbd> + <kbd>R</kbd>.
      2. Type `wt` or `wt.exe`.
      3. Press <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>Enter</kbd>.
      4. When the User Account Control (UAC) prompt appears, approve it.

      Method 4: Open as administrator from the Start menu

      1. Press the <kbd>Windows</kbd> key.
      2. Open the list of all installed apps.
      3. Search for `Terminal`.
      4. Right-click **Terminal**.
      5. Select **Run as administrator**.
      6. When the User Account Control (UAC) prompt appears, approve it.

      Method 5: Open an administrator tab from Windows Terminal

      1. Open Windows Terminal normally.
      2. Click the down arrow (`∨`) next to the tab bar.
      3. Right-click **PowerShell**.
      4. Select **Run as administrator**.
      5. When the User Account Control (UAC) prompt appears, approve it.

## Install Claw Code

### 1. Install or update PowerShell

1. Open Windows Terminal with **Command Prompt**.
      - Click the down arrow (`∨`) and choose **Command Prompt**.
2. Run the following command:

      ```cmd
      winget upgrade Microsoft.PowerShell --source winget
      ```

      If PowerShell is not installed yet, run this instead:

      ```cmd
      winget install Microsoft.PowerShell --source winget
      ```

3. Restart Windows Terminal.
4. Open **Settings** from the down arrow (`∨`) menu.
5. In **Startup**, set **Default profile** to **PowerShell**.

    !!! note
        Choose **PowerShell**, not **Windows PowerShell**.
        **Windows PowerShell** is the older version included with Windows.

6. Open a new PowerShell tab and confirm that it shows PowerShell 7 or newer.

### 2. Install Git

Run the following command:

```powershell
winget install Git.Git --source winget
```

After installation, restart Windows Terminal.

### 3. Clone the repository

1. Move to the directory where you want to place the source code.
   This guide uses the `Documents` folder:

      ```powershell
      Set-Location $env:USERPROFILE\Documents
      ```

2. Clone the repository:

      ```powershell
      git clone https://github.com/ultraworkers/claw-code.git
      ```

3. Move into the cloned repository:

      ```powershell
      Set-Location .\claw-code\rust
      ```

### 4. Install Rust

Run the following command:

```powershell
winget install Rustlang.Rustup --source winget
```

After installation, restart Windows Terminal.

### 5. Build Claw Code from source

From the cloned `claw-code\rust` directory, run one of the following commands.

For a debug build:

```powershell
cargo build --workspace
```

For an optimized release build:

```powershell
cargo build --release
```

The build may take about 5 to 15 minutes.

- `cargo build --workspace` is usually faster to build, but the executable is a debug build.
- `cargo build --release` takes longer, but the executable starts faster and has a smaller file size.

## Set API keys

Claw Code needs API credentials for the model provider you want to use.

### Method 1: Set environment variables for the current terminal only

This method is safer because the values disappear when you close the terminal.

For Anthropic Claude models, run one of the following commands:

```powershell
$env:ANTHROPIC_API_KEY = "SET YOUR API KEY HERE"
```

or:

```powershell
$env:ANTHROPIC_AUTH_TOKEN = "SET YOUR AUTH TOKEN HERE"
```

For OpenAI-compatible APIs, run:

```powershell
$env:OPENAI_API_KEY = "SET YOUR API KEY HERE"
$env:OPENAI_BASE_URL = "SET YOUR API ENDPOINT HERE"
```

Examples of OpenAI-compatible API endpoints:

- `https://openrouter.ai/api/v1`
- `https://api.cerebras.ai/v1`
- `http://127.0.0.1:8000/v1` for local models such as Ollama, llama.cpp, or LM Studio

Then start Claw Code. Use the command that matches the build type you created.

For a debug build:

```powershell
.\target\debug\claw.exe --model "YOUR AI MODEL"
```

For a release build:

```powershell
.\target\release\claw.exe --model "YOUR AI MODEL"
```

Example model names:

- `openai/gpt-5.5`
- `anthropic/claude-opus-4-7`
- `deepseek/deepseek-v4-pro`

### Method 2: Save environment variables permanently

This method is convenient for daily use, but it stores your credentials in your user environment variables.
Use it only if you understand the security risk.

```powershell
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "SET YOUR API KEY HERE", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "SET YOUR API ENDPOINT HERE", "User")
```

Open a new terminal after setting permanent environment variables.

Then start Claw Code. Use the command that matches the build type you created.

For a debug build:

```powershell
.\target\debug\claw.exe --model "YOUR AI MODEL"
```

For a release build:

```powershell
.\target\release\claw.exe --model "YOUR AI MODEL"
```

## Add `claw.exe` to your PATH

If you want to run `claw` from any directory, add the build output directory to your user `Path` environment variable.

For a debug build:

```powershell
$newPath = "$env:USERPROFILE\Documents\claw-code\rust\target\debug"
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$oldPath;$newPath", "User")
```

For a release build, use `target\release` instead:

```powershell
$newPath = "$env:USERPROFILE\Documents\claw-code\rust\target\release"
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$oldPath;$newPath", "User")
```

Open a new terminal after updating `Path`.

## Start Claw Code in your project

1. Move to your own project repository:

      ```powershell
      Set-Location $env:USERPROFILE\Documents\my-repository
      ```

2. Start Claw Code:

      ```powershell
      claw --model "openai/gpt-5.5"
      ```

Replace the model name with the model you want to use.
