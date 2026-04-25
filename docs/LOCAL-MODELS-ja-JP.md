# Windowsでllama.cppのローカルモデルを使う

このページでは、`llama.cpp`を使ってローカルLLMを起動し、OpenAI互換APIとしてClaw Codeから利用する方法を説明します。

目標は次の流れです。

1. `llama.cpp`を導入する。
2. `huggingface-cli`でHugging FaceからGGUFモデルをダウンロードする。
3. `llama-server.exe`をローカルで起動する。
4. Claw Codeの`OPENAI_BASE_URL`をローカルサーバーに向けて起動する。

!!! note
    ローカルモデルは有料APIクレジットなしで使えますが、CPU、RAM、できれば高性能なGPUと大容量のVRAMが必要です。大きいモデルほど遅くなり、必要メモリも増えます。

## 推奨フォルダ構成

このページでは、次のフォルダ構成を例にします。

```txt
%USERPROFILE%\Documents\local-ai\
├── llama.cpp\
│   └── llama-server.exe
└── models\
    └── qwen3.6-35b\
        └── model.gguf
```

まず、作業用フォルダを作成します。

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\Documents\local-ai"
Set-Location "$env:USERPROFILE\Documents\local-ai"
```

## 1. llama.cppを導入する

Windowsで`llama.cpp`を導入する方法は、大きく分けて2つあります。

### 方法A: ビルド済みリリースをダウンロードする

初心者にはこの方法が簡単です。

1. llama.cppのリリースページを開きます。
   - https://github.com/ggml-org/llama.cpp/releases
2. 自分の環境に合うWindows用のアーカイブをダウンロードします。
   - CPUのみで使う場合は、`llama-server.exe`を含むWindows用アーカイブを選びます。`llama-(ランダム文字列)-bin-win-cpu-x64.zip`のようなファイル名です
   - NVIDIA GPUを使う場合は、CUDA対応のWindows用アーカイブがあれば、自分のCUDAドライバーに合うものを選びます。`cudart-llama-bin-win-cuda-12.4-x64.zip`や`cudart-llama-bin-win-cuda-13.1-x64.zip`、`llama-(ランダム文字列)-bin-win-cuda-12.4-x64.zip`、`llama-(ランダム文字列)-bin-win-cuda-13.1-x64.zip`のようなファイル名です。
3. ダウンロードしたアーカイブを展開します。
4. 展開したフォルダを次の場所にコピーまたは移動します。

   ```txt
   %USERPROFILE%\Documents\local-ai\llama.cpp
   ```

5. `llama-server.exe`が存在することを確認します。

   ```powershell
   Test-Path "$env:USERPROFILE\Documents\local-ai\llama.cpp\llama-server.exe"
   ```

`True`と表示されれば、実行ファイルを利用できます。

### 方法B: ソースコードからビルドする

特定のビルドオプションが必要な場合や、ビルド済みバイナリが合わない場合はこちらを使います。

必要なツールをインストールします。

```powershell
winget install Git.Git --source winget
winget install Kitware.CMake --source winget
winget install Microsoft.VisualStudio.2022.BuildTools --source winget
```

インストール後、Windows Terminalを再起動してください。

次に`llama.cpp`をクローンしてビルドします。

```powershell
Set-Location "$env:USERPROFILE\Documents\local-ai"
git clone https://github.com/ggml-org/llama.cpp.git
Set-Location .\llama.cpp
cmake -B build
cmake --build build --config Release
```

ビルド後、`llama-server.exe`を探します。実際の場所はビルド設定によって変わることがありますが、`bin\Release`配下などに生成されることが多いです。

```powershell
Get-ChildItem -Recurse -Filter llama-server.exe
```

## 2. hfをインストールする

`hf`はPythonパッケージの`huggingface_hub`に含まれています。

```powershell
python -m pip install -U huggingface_hub
```

コマンドが使えるか確認します。

```powershell
hf --help
```

`hf`が見つからない場合は、Windows Terminalを閉じて開き直してから再度試してください。

## 3. 必要に応じてHugging Faceへログインする

モデルによっては、ダウンロード前にライセンス同意やログインが必要です。

1. Hugging Faceのアカウントを作成、またはログインします。
2. モデルページで利用条件への同意が必要な場合は、ブラウザ上で同意します。
3. Hugging Faceでアクセストークンを作成します。
4. PowerShellでログインします。

```powershell
hf login
```

プロンプトが表示されたら、アクセストークンを貼り付けます。

!!! warning
    Hugging Faceのトークンを、公開リポジトリ、スクリーンショット、共有ログなどに貼り付けないでください。

## 4. GGUFモデルを選ぶ

`llama.cpp`で使う場合は、GGUF形式のモデルを選びます。

モデル選定時は、次の点を確認します。

- **形式**: ファイル名が`.gguf`で終わるものを選びます。
- **Instruction tuning**: Claw Code用途では`Instruct`または`Coder`系のモデルが向いています。
- **量子化**: ローカル用途では`Q4_K_M`がバランスのよい選択肢です。量子化の数字が小さいほど、使用メモリが少なくなります。Q8はメモリを多く使用し、Q4はメモリが少なくなります。
- **メモリ**: 大きいモデルほど多くのRAMまたはVRAMが必要です。モデルのサイズは、27bや35b、500Mなどの表記になっています。

Hugging Faceでは、たとえば次のようなキーワードで探します。

- Qwen 3.6 GGUF
- DeepSeek R1 GGUF
- Distill GGUF
- Mistral or Mixtral Instruct GGUF

最初の動作確認では、7Bまたは8B程度の`Instruct`/`Coder`モデルで、`Q4_K_M`量子化のものを選ぶと扱いやすいです。

## 5. hfでモデルをダウンロードする

モデル保存用フォルダを作成します。

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\Documents\local-ai\models"
Set-Location "$env:USERPROFILE\Documents\local-ai\models"
```

特定のGGUFファイルをダウンロードします。

```powershell
hf download REPOSITORY_NAME FILE_NAME.gguf --local-dir .\MODEL_FOLDER
```

置き換える値は次のとおりです。

- `REPOSITORY_NAME`: Hugging Faceのリポジトリ名。例: `owner/model-repository`
- `FILE_NAME.gguf`: ダウンロードしたいGGUFファイル名
- `MODEL_FOLDER`: ローカル保存先フォルダ名

例:

```powershell
hf download unsloth/Qwen3.6-35B-A3B-GGUF Qwen3.6-35B-A3B-UD-IQ4_XS.gguf --local-dir .\qwen3.6-35b-iq4
```

ダウンロード後、ファイルが存在することを確認します。

```powershell
Get-ChildItem .\qwen3.6-35b-iq4 -Filter *.gguf
```

扱いやすいように、モデルファイル名を`model.gguf`へ変更しておくこともできます。

```powershell
Rename-Item .\qwen3.6-35b-iq4\Qwen3.6-35B-A3B-UD-IQ4_XS.gguf model.gguf
```

## 6. llama-server.exeを起動する

`llama.cpp`のフォルダへ移動します。

```powershell
Set-Location "$env:USERPROFILE\Documents\local-ai\llama.cpp"
```

サーバーを起動します。

```powershell
.\llama-server.exe `
  -m "$env:USERPROFILE\Documents\local-ai\models\qwen3.6-35b-iq4\model.gguf" `
  --host 127.0.0.1 `
  --port 8000 `
  --alias qwen3.6-35b-iq4-local `
  -c 8192
```

各オプションの意味は次のとおりです。

- `-m`: GGUFモデルファイルのパス
- `--host 127.0.0.1`: 自分のPCからのみ接続を受け付ける
- `--port 8000`: 8000番ポートでサーバーを公開する
- `--alias`: OpenAI互換クライアントから使うモデル名
- `-c 8192`: コンテキストサイズ

ローカルモデルを使っている間は、このPowerShellウィンドウを開いたままにします。

!!! tip
    メモリ不足になる場合は、より小さいモデルを使う、`Q4_K_M`や`Q3_K_M`など軽い量子化を選ぶ、または`-c 4096`のようにコンテキストサイズを下げてください。

## 7. ローカルのOpenAI互換エンドポイントをテストする

別のPowerShellウィンドウを開いて、次を実行します。

```powershell
Invoke-RestMethod `
  -Uri "http://127.0.0.1:8000/v1/chat/completions" `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"model":"qwen3.6-35b-iq4-local","messages":[{"role":"user","content":"Say hello in one sentence."}]}'
```

正常に動作していれば、ローカルモデルからの応答が返ります。

## 8. Claw Codeからllama-server.exeへ接続する

Claw Codeを起動するPowerShellウィンドウで、OpenAI互換API用の環境変数を設定します。

```powershell
$env:OPENAI_API_KEY = "local"
$env:OPENAI_BASE_URL = "http://127.0.0.1:8000/v1"
```

その後、`llama-server.exe`起動時に指定したモデルエイリアスを使ってClaw Codeを起動します。
プロバイダー名はAPIの種別特定に使われるので`openai/`を付与します。

Debugビルドの場合:

```powershell
.\target\debug\claw.exe --model "openai/qwen3.6-35b-iq4-local"
```

Releaseビルドの場合:

```powershell
.\target\release\claw.exe --model "openai/qwen3.6-35b-iq4-local"
```

すでに`claw.exe`へ`Path`を通している場合は、次のように実行できます。

```powershell
claw --model "openai/qwen3.6-35b-iq4-local"
```

## トラブルシューティング

### `llama-server.exe`が見つからない

展開先またはビルド先を確認します。

```powershell
Get-ChildItem "$env:USERPROFILE\Documents\local-ai" -Recurse -Filter llama-server.exe
```

見つかった`llama-server.exe`があるフォルダへ移動して実行してください。

### モデルが遅すぎる

次を試してください。

- より小さいモデルを使う。
- より軽い量子化のモデルを使う。
- `-c 4096`のようにコンテキストサイズを下げる。
- 対応GPUがある場合は、GPU対応のllama.cppビルドを使う。

### Claw Codeから接続できない

`llama-server.exe`が起動したままになっているか、`OPENAI_BASE_URL`に`/v1`が含まれているか確認します。

```powershell
$env:OPENAI_BASE_URL
```

期待する値:

```txt
http://127.0.0.1:8000/v1
```

### 誤って外部公開しない

通常のローカル利用では、`--host 127.0.0.1`のままにしてください。

`--host 0.0.0.0`を使うと外部の端末から接続できる可能性があります。セキュリティ上の意味を理解し、ファイアウォール設定を行える場合以外は使わないでください。
