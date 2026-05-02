# Windowsでllama.cppのローカルモデルを使う

このページでは、`llama.cpp`を使ってローカルLLMを起動し、OpenAI互換APIとしてClaw Codeから利用する方法を説明します。

目標は次の流れです。

1. `llama.cpp`を導入する。
2. NVIDIA GPUを使う場合はCUDAをインストールする。
3. Pythonをインストールする。
4. `hf`をインストールし、Hugging FaceからGGUFモデルをダウンロードする。
5. `llama-server.exe`をローカルで起動する。
6. Claw Codeの`OPENAI_BASE_URL`をローカルサーバーに向けて起動する。

!!! note
    ローカルモデルは有料APIクレジットなしで使えますが、CPU、RAM、できれば高性能なGPUと大容量のVRAMが必要です。大きいモデルほど遅くなり、必要メモリも増えます。

!!! warning
    小さいローカルモデルは、コーディングエージェント用途では十分に動作しないことがあります。出力崩壊を起こす、エージェント的な作業でターミナルを適切に使えない、存在しないコマンド・ファイル・API・実行結果をハルシネーションする、といった問題が起こり得ます。可能であれば、用途に対して十分なサイズの`Instruct`系または`Coder`系モデルを使ってください。

## 推奨フォルダ構成

このページでは、次のフォルダ構成を例にします。

```txt
%USERPROFILE%\Documents\local-ai\
├── llama.cpp\
│   └── llama-server.exe
└── models\
    └── devstral-small-2507-q4km\
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
    - [llama.cpp releases](https://github.com/ggml-org/llama.cpp/releases)
2. 自分の環境に合うWindows用のアーカイブをダウンロードします。
    - CPUのみで使う場合は、`llama-server.exe`を含むWindows用アーカイブを選びます。`llama-(ランダム文字列)-bin-win-cpu-x64.zip`のようなファイル名です。
    - NVIDIA GPUで推論する場合は、CUDA対応のWindows用アーカイブを選び、対応するCUDAランタイムを入れます。`cudart-llama-bin-win-cuda-12.4-x64.zip`、`cudart-llama-bin-win-cuda-13.1-x64.zip`、`llama-(ランダム文字列)-bin-win-cuda-12.4-x64.zip`、`llama-(ランダム文字列)-bin-win-cuda-13.1-x64.zip`のようなファイル名です。
    - NVIDIA以外のGPUバックエンドを使う場合は、`llama.cpp`のWindowsビルドにはVulkan、SYCL、HIPもあります。
3. ダウンロードしたアーカイブを展開します。
4. 展開したフォルダを次の場所にコピーまたは移動します。

    ```txt
    %USERPROFILE%\Documents\local-ai\llama.cpp
    ```

5. `cudart-llama-bin-win-cuda-<version>-x64.zip`をダウンロードし、DLLファイルを`llama-server.exe`の隣にある`llama.cpp`フォルダへ展開します。
6. `llama-server.exe`が存在することを確認します。

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

## 2. NVIDIA GPUを使う場合はCUDAをインストールする

NVIDIA GPU対応の`llama.cpp`ビルドを使う場合は、GPUドライバーに加えてCUDAランタイムまたはCUDA Toolkitが必要です。Vulkan、SYCL、HIPのビルドを使う場合はCUDAは不要で、対応するバックエンドのアーカイブを使います。

まず、利用できるCUDAのバージョンを確認します。

```powershell
winget show Nvidia.CUDA --versions
```

### CUDA 13をインストールする場合

CUDA 13系でよい場合は、次のコマンドでインストールできます。

```powershell
winget install Nvidia.CUDA --source winget
```

!!! warning
    `winget install Nvidia.CUDA`を実行すると、wingetで公開されている最新のCUDAがインストールされます。現在はCUDA 13.2がインストールされる場合があります。CUDA 12系が必要な場合は、このコマンドをそのまま実行せず、次の手順でバージョンを指定してください。

### CUDA 12をインストールする場合

CUDA 12対応の`llama.cpp`アーカイブを使う場合は、`winget show Nvidia.CUDA --versions`で表示されたCUDA 12系のバージョンを指定してインストールします。

```powershell
winget install Nvidia.CUDA --source winget --version 12.9
```

インストール後、Windows Terminalを再起動してから確認します。

```powershell
nvcc --version
```

!!! tip
    ビルド済みアーカイブのファイル名に`cuda-12.4`や`cuda-13.1`のような表記がある場合は、できるだけ近いCUDAメジャーバージョンを使ってください。CUDA 12用のアーカイブにはCUDA 12系、CUDA 13用のアーカイブにはCUDA 13系を合わせるとトラブルを減らせます。

## 3. Pythonをインストールする

`hf`はPythonパッケージとして配布されているため、`huggingface_hub`をインストールする前にPythonを導入します。

wingetでPythonをインストールします。

```powershell
winget install Python.Python.3.12 --source winget
```

インストール後、Windows Terminalを閉じて開き直します。その後、Pythonとpipが使えることを確認します。

```powershell
python --version
python -m pip --version
```

`python`が見つからない場合は、もう一度Windows Terminalを再起動してください。それでも見つからない場合は、Pythonが`Path`に追加されているか確認してください。

## 4. hfをインストールする

`hf`はPythonパッケージの`huggingface_hub`に含まれています。

```powershell
python -m pip install -U huggingface_hub
```

コマンドが使えるか確認します。

```powershell
hf --help
```

`hf`が見つからない場合は、Windows Terminalを閉じて開き直してから再度試してください。それでも見つからない場合は、Pythonの`Scripts`フォルダが`Path`に追加されているか確認してください。

## 5. 必要に応じてHugging Faceへログインする

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

## 6. GGUFモデルを選ぶ

`llama.cpp`で使う場合は、GGUF形式のモデルを選びます。

モデル選定時は、次の点を確認します。

- **形式**: ファイル名が`.gguf`で終わるものを選びます。
- **Instruction tuning**: Claw Code用途では`Instruct`または`Coder`系のモデルが向いています。
- **量子化**: ローカル用途では`Q4_K_M`がバランスのよい選択肢です。量子化の数字が小さいほど、使用メモリが少なくなります。Q8はメモリを多く使用し、Q4はメモリが少なくなります。
- **メモリ**: 大きいモデルほど多くのRAMまたはVRAMが必要です。モデルのサイズは、27B、35B、500Mなどの表記になっています。

2026/04時点では、実在するGGUFリポジトリだけを書き、しかも選ぶ量子化がVRAM帯に収まるものだけを載せます。モデルカードにある実際のファイルサイズを見て、対象VRAMに少し余裕があるものを選んでください。

今回の調査結果では、`Devstral-Small-2507`、`Qwen3-Coder-30B-A3B-Instruct`、`Qwen2.5-Coder-14B-Instruct`、`Qwen2.5-Coder-7B-Instruct`、そして長文効率を重視する場合の`DeepSeek-Coder-V2-Lite-Instruct`が、実運用での有力候補です。

VRAM別のおすすめは次のとおりです。

| VRAM | モデル | GGUF量子化 | 使いどころ | 備考 |
| --- | --- | --- | --- | --- |
| 32 GB | `mistralai/Devstral-Small-2507_gguf` | `Q5_K_M` | 128k native / 約90k程度まで現実的 | レポートの最有力。エージェント用途の本命です。 |
| 32 GB | `unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF` | `Q4_K_M` | 262k native / 100k超級も狙いやすい | 長文とツール利用を重視するなら強い候補です。 |
| 24 GB | `mistralai/Devstral-Small-2507_gguf` | `Q4_K_M` | 128k native / 余裕を残しやすい | もっとも堅い選択です。 |
| 24 GB | `unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF` | `Q4_K_M` | 262k native / 3万token超級 | MoE系の最新候補として有力です。 |
| 16 GB | `Qwen/Qwen2.5-Coder-14B-Instruct-GGUF` | `Q4_K_M` | フル32,768 tokens / 実用24k-30k程度 | 密なコーダーで、16 GB帯の安全策です。 |
| 16 GB | `DeepSeek-Coder-V2-Lite-Instruct` | `Q4_K_M` または `Q6_K` | 長文効率重視 / GGUFでは実装差あり | VRAMの単純比較よりも長文効率を優先する場合に候補です。 |
| 12 GB | `Qwen/Qwen2.5-Coder-7B-Instruct-GGUF` | `Q4_K_M` | フル32,768 tokens | 12 GB帯で最も無難です。 |
| 12 GB | `DeepSeek-Coder-V2-Lite-Instruct` | `Q4_K_M` | 長文効率重視 / GGUFでは実装差あり | 長文資料やリポジトリ作業向きの代替候補です。 |
| 8 GB | `Qwen/Qwen2.5-Coder-7B-Instruct-GGUF` | `Q4_K_M` | フル32,768 tokens | 8 GB帯の安全策です。 |

`llama.cpp`はVRAMに収まらないモデルをRAMへ逃がして動かすこともできますが、このガイドの主な例では扱いません。速度が落ち、VRAM帯ごとの比較もしづらくなるためです。

Hugging Faceでは、たとえば次のようなキーワードで探します。

- `Devstral-Small-2507 GGUF`
- `Qwen3-Coder-30B-A3B-Instruct GGUF`
- `DeepSeek-Coder-V2-Lite-Instruct GGUF`
- `Qwen2.5-Coder-14B-Instruct GGUF`
- `Qwen2.5-Coder-7B-Instruct GGUF`
- `Qwen2.5-Coder-32B-Instruct GGUF`

最初の動作確認では、`Qwen2.5-Coder-7B-Instruct-GGUF`の`Q4_K_M`量子化がいちばん安全で扱いやすいです。Claw Codeの出力が崩れる、存在しないコマンドを作る、ターミナル作業を進められないといった場合は、`Qwen2.5-Coder-14B-Instruct-GGUF`か`Devstral-Small-2507`へ上げてください。

自分が試した範囲ではunsloth/gpt-oss-20b-Q8_0.ggufが最も上手くターミナルベースワークフローを扱えた。

## 7. hfでモデルをダウンロードする

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
hf download mistralai/Devstral-Small-2507_gguf Devstral-Small-2507-Q4_K_M.gguf --local-dir .\devstral-small-2507-q4km
```

ダウンロード後、ファイルが存在することを確認します。

```powershell
Get-ChildItem .\devstral-small-2507-q4km -Filter *.gguf
```

扱いやすいように、モデルファイル名を`model.gguf`へ変更しておくこともできます。

```powershell
Rename-Item .\devstral-small-2507-q4km\Devstral-Small-2507-Q4_K_M.gguf model.gguf
```

## 8. llama-server.exeを起動する

`llama.cpp`のフォルダへ移動します。

```powershell
Set-Location "$env:USERPROFILE\Documents\local-ai\llama.cpp"
```

サーバーを起動します。

```powershell
.\llama-server.exe `
  -m "$env:USERPROFILE\Documents\local-ai\models\devstral-small-2507-q4km\model.gguf" `
  --host 127.0.0.1 `
  --port 8000 `
  --alias devstral-small-2507-q4km-local `
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

## 9. ローカルのOpenAI互換エンドポイントをテストする

別のPowerShellウィンドウを開いて、次を実行します。

```powershell
Invoke-RestMethod `
  -Uri "http://127.0.0.1:8000/v1/chat/completions" `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"model":"devstral-small-2507-q4km-local","messages":[{"role":"user","content":"Say hello in one sentence."}]}'
```

正常に動作していれば、ローカルモデルからの応答が返ります。

## 10. Claw Codeからllama-server.exeへ接続する

Claw Codeを起動するPowerShellウィンドウで、OpenAI互換API用の環境変数を設定します。

```powershell
$env:OPENAI_API_KEY = "local"
$env:OPENAI_BASE_URL = "http://127.0.0.1:8000/v1"
```

もしまだ Claw Code の `rust` ディレクトリにいない場合は、先に移動してください。例:

```powershell
Set-Location "$env:USERPROFILE\Documents\claw-code\rust"
```

または親フォルダにいる場合は相対パスで移動できます。

```powershell
Set-Location .\claw-code\rust
```

その後、`llama-server.exe`起動時に指定したモデルエイリアスを使ってClaw Codeを起動します。プロバイダー名はAPIの種別特定に使われるので、モデル名には`openai/`を付与します。

Debugビルドの場合:

```powershell
.\target\debug\claw.exe --model "openai/devstral-small-2507-q4km-local"
```

Releaseビルドの場合:

```powershell
.\target\release\claw.exe --model "openai/devstral-small-2507-q4km-local"
```

すでに`claw.exe`へ`Path`を通している場合は、次のように実行できます。

```powershell
claw --model "openai/devstral-small-2507-q4km-local"
```

## トラブルシューティング

### `llama-server.exe`が見つからない

展開先またはビルド先を確認します。

```powershell
Get-ChildItem "$env:USERPROFILE\Documents\local-ai" -Recurse -Filter llama-server.exe
```

見つかった`llama-server.exe`があるフォルダへ移動して実行してください。

ダウンロードした`cudart-llama-bin-win-cuda-<version>-x64.zip`に含まれるCUDAランタイムのDLLファイルが、`llama-server.exe`と同じ`llama.cpp`フォルダに展開されていることも確認してください。

### CUDAを入れたのにGPUが使われない

次を確認してください。

- NVIDIAドライバーがインストールされている。
- CUDA対応の`llama.cpp`アーカイブを使っている。CPU専用、Vulkan、SYCL、HIPのビルドではありません。
- CUDA 12用アーカイブにはCUDA 12系、CUDA 13用アーカイブにはCUDA 13系を使っている。
- Windows Terminalを再起動してから`nvcc --version`を確認している。

CUDAビルドが起動直後に落ちる場合は、`cudart-llama-bin-win-cuda-<version>-x64.zip`のDLLを`llama.cpp`フォルダへ展開したかも再確認してください。

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
