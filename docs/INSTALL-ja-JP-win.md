# WindowsでClaw Codeをインストールする方法

このガイドでは、WindowsでClaw Codeをインストールして実行する手順を説明します。
初心者向けに、Windows TerminalとPowerShellを使って進めます。

## Windows Terminalを開く

次のいずれかの方法でWindows Terminalを開けます。

### 方法1: 「ファイル名を指定して実行」から開く

1. <kbd>Windows</kbd> + <kbd>R</kbd> を押します。
2. `wt` または `wt.exe` と入力します。
3. <kbd>Enter</kbd> を押します。

### 方法2: スタートメニューから開く

1. <kbd>Windows</kbd> キーを押します。
2. インストール済みアプリの一覧を開きます。
3. `Terminal` を検索します。
4. **Terminal** をクリックします。

??? info "管理者として開く"

      方法3: 「ファイル名を指定して実行」から管理者として開く

      1. <kbd>Windows</kbd> + <kbd>R</kbd> を押します。
      2. `wt` または `wt.exe` と入力します。
      3. <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>Enter</kbd> を押します。
      4. ユーザーアカウント制御（UAC）が表示されたら許可します。

      方法4: スタートメニューから管理者として開く

      1. <kbd>Windows</kbd> キーを押します。
      2. インストール済みアプリの一覧を開きます。
      3. `Terminal` を検索します。
      4. **Terminal** を右クリックします。
      5. **管理者として実行** を選択します。
      6. ユーザーアカウント制御（UAC）が表示されたら許可します。

      方法5: Windows Terminalから管理者タブを開く

      1. Windows Terminalを通常どおり開きます。
      2. タブバーの横にある下向き矢印（`∨`）をクリックします。
      3. **PowerShell** を右クリックします。
      4. **管理者として実行** を選択します。
      5. ユーザーアカウント制御（UAC）が表示されたら許可します。

## Claw Codeをインストールする

### 1. PowerShellをインストールまたは更新する

1. Windows Terminalで **Command Prompt** を開きます。
      - 下向き矢印（`∨`）をクリックして **Command Prompt** を選びます。
2. 次のコマンドを実行します。

      ```cmd
      winget upgrade Microsoft.PowerShell --source winget
      ```

      PowerShellがまだインストールされていない場合は、代わりに次のコマンドを実行します。

      ```cmd
      winget install Microsoft.PowerShell --source winget
      ```

3. Windows Terminalを再起動します。
4. 下向き矢印（`∨`）メニューから **Settings** を開きます。
5. **Startup** で **Default profile** を **PowerShell** に設定します。

    !!! note
        **Windows PowerShell** ではなく **PowerShell** を選んでください。
        **Windows PowerShell** はWindowsに標準搭載されている古いPowerShellです。

6. 新しいPowerShellタブを開き、PowerShell 7以降が表示されることを確認します。

### 2. Gitをインストールする

次のコマンドを実行します。

```powershell
winget install Git.Git --source winget
```

インストール後、Windows Terminalを再起動します。

### 3. リポジトリをクローンする

1. ソースコードを置きたいディレクトリへ移動します。
   このガイドでは `Documents` フォルダを使います。

      ```powershell
      Set-Location $env:USERPROFILE\Documents
      ```

2. リポジトリをクローンします。

      ```powershell
      git clone https://github.com/ultraworkers/claw-code.git
      ```

3. クローンしたリポジトリへ移動します。

      ```powershell
      Set-Location .\claw-code\rust
      ```

### 4. Rustをインストールする

次のコマンドを実行します。

```powershell
winget install Rustlang.Rustup --source winget
```

インストール後、Windows Terminalを再起動します。

### 5. ソースコードからClaw Codeをビルドする

クローンした `claw-code\rust` ディレクトリ内で、次のどちらかのコマンドを実行します。

デバッグビルドの場合:

```powershell
cargo build --workspace
```

最適化されたリリースビルドの場合:

```powershell
cargo build --release
```

ビルドには5〜15分ほどかかる場合があります。

- `cargo build --workspace` は通常ビルドが速いですが、実行ファイルはデバッグビルドです。
- `cargo build --release` はビルドに時間がかかりますが、実行ファイルの起動が速く、ファイルサイズも小さくなります。

## APIキーを設定する

Claw Codeを使うには、利用するモデルプロバイダーのAPI認証情報が必要です。

### 方法1: 現在のターミナルだけに環境変数を設定する

この方法は、ターミナルを閉じると値が消えるため比較的安全です。

Anthropic Claudeモデルを使う場合は、次のどちらかを実行します。

```powershell
$env:ANTHROPIC_API_KEY = "SET YOUR API KEY HERE"
```

または:

```powershell
$env:ANTHROPIC_AUTH_TOKEN = "SET YOUR AUTH TOKEN HERE"
```

OpenAI互換APIを使う場合は、次のように設定します。

```powershell
$env:OPENAI_API_KEY = "SET YOUR API KEY HERE"
$env:OPENAI_BASE_URL = "SET YOUR API ENDPOINT HERE"
```

OpenAI互換APIエンドポイントの例:

- `https://openrouter.ai/api/v1`
- `https://api.cerebras.ai/v1`
- ローカルモデル（Ollama、llama.cpp、LM Studioなど）では `http://127.0.0.1:8000/v1`

その後、ビルドの種類に合ったコマンドでClaw Codeを起動します。

デバッグビルドの場合:

```powershell
.\target\debug\claw.exe --model "YOUR AI MODEL"
```

リリースビルドの場合:

```powershell
.\target\release\claw.exe --model "YOUR AI MODEL"
```

モデル名の例:

- `openai/gpt-5.5`
- `anthropic/claude-opus-4-7`
- `deepseek/deepseek-v4-pro`

### 方法2: 環境変数を永続的に保存する

この方法は日常利用には便利ですが、認証情報がユーザー環境変数に保存されます。
セキュリティ上のリスクを理解したうえで使用してください。

```powershell
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "SET YOUR API KEY HERE", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "SET YOUR API ENDPOINT HERE", "User")
```

永続的な環境変数を設定した後は、新しいターミナルを開いてください。

その後、ビルドの種類に合ったコマンドでClaw Codeを起動します。

デバッグビルドの場合:

```powershell
.\target\debug\claw.exe --model "YOUR AI MODEL"
```

リリースビルドの場合:

```powershell
.\target\release\claw.exe --model "YOUR AI MODEL"
```

## `claw.exe` をPATHに追加する

任意のディレクトリから `claw` コマンドを実行したい場合は、ビルド出力ディレクトリをユーザーの `Path` 環境変数に追加します。

デバッグビルドの場合:

```powershell
$newPath = "$env:USERPROFILE\Documents\claw-code\rust\target\debug"
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$oldPath;$newPath", "User")
```

リリースビルドの場合は、`target\release` を使います。

```powershell
$newPath = "$env:USERPROFILE\Documents\claw-code\rust\target\release"
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$oldPath;$newPath", "User")
```

`Path` を更新した後は、新しいターミナルを開いてください。

## 自分のプロジェクトでClaw Codeを起動する

1. 自分のプロジェクトリポジトリへ移動します。

      ```powershell
      Set-Location $env:USERPROFILE\Documents\my-repository
      ```

2. Claw Codeを起動します。

      ```powershell
      claw --model "openai/gpt-5.5"
      ```

モデル名は、使いたいモデルに置き換えてください。
