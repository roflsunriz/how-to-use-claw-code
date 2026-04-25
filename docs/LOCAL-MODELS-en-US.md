# Local Models with llama.cpp on Windows

This guide explains how to run a local model with `llama.cpp` and connect Claw Code to it through an OpenAI-compatible local endpoint.

The goal is to run the following flow:

1. Install or obtain `llama.cpp`.
2. Download a GGUF model from Hugging Face with `huggingface-cli`.
3. Start `llama-server.exe` locally.
4. Start Claw Code with `OPENAI_BASE_URL` pointing to the local server.

!!! note
    Local models do not require paid API credits, but they require enough CPU, RAM, and preferably VRAM. Larger models are slower and require more memory.

## Recommended folder layout

This guide uses the following folders:

```txt
%USERPROFILE%\Documents\local-ai\
├── llama.cpp\
│   └── llama-server.exe
└── models\
    └── qwen2.5-coder-7b\
        └── model.gguf
```

Create the base directory:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\Documents\local-ai"
Set-Location "$env:USERPROFILE\Documents\local-ai"
```

## 1. Install llama.cpp

There are two common ways to obtain `llama.cpp` on Windows.

### Option A: Download a prebuilt release

This is the easiest method.

1. Open the llama.cpp releases page:
   - https://github.com/ggml-org/llama.cpp/releases
2. Download a Windows build that matches your environment.
   - For CPU-only usage, choose a Windows archive that includes `llama-server.exe`.
   - For NVIDIA GPU usage, choose a CUDA-enabled Windows archive if available and compatible with your CUDA driver.
3. Extract the archive.
4. Copy or move the extracted folder to:

   ```txt
   %USERPROFILE%\Documents\local-ai\llama.cpp
   ```

5. Confirm that `llama-server.exe` exists:

   ```powershell
   Test-Path "$env:USERPROFILE\Documents\local-ai\llama.cpp\llama-server.exe"
   ```

If the command returns `True`, the executable is available.

### Option B: Build llama.cpp from source

Use this method if you need a specific build option or if a prebuilt binary is not suitable.

Install the required tools:

```powershell
winget install Git.Git --source winget
winget install Kitware.CMake --source winget
winget install Microsoft.VisualStudio.2022.BuildTools --source winget
```

Restart Windows Terminal after installing these tools.

Then clone and build `llama.cpp`:

```powershell
Set-Location "$env:USERPROFILE\Documents\local-ai"
git clone https://github.com/ggml-org/llama.cpp.git
Set-Location .\llama.cpp
cmake -B build
cmake --build build --config Release
```

After the build completes, find `llama-server.exe` under the build directory. The exact path can vary depending on the build configuration, but it is commonly under a `bin\Release` directory.

For example:

```powershell
Get-ChildItem -Recurse -Filter llama-server.exe
```

## 2. Install huggingface-cli

`huggingface-cli` is included in the `huggingface_hub` Python package.

```powershell
python -m pip install -U huggingface_hub
```

Check that the command is available:

```powershell
huggingface-cli --help
```

If `huggingface-cli` is not found, close and reopen Windows Terminal, then try again.

## 3. Log in to Hugging Face when needed

Some models require accepting a license or logging in before download.

1. Create or open your Hugging Face account.
2. If the model page requires accepting terms, accept them in the browser.
3. Create an access token on Hugging Face.
4. Log in from PowerShell:

```powershell
huggingface-cli login
```

Paste your token when prompted.

!!! warning
    Do not paste your Hugging Face token into public repositories, screenshots, or shared logs.

## 4. Choose a GGUF model

For `llama.cpp`, choose a model in the GGUF format.

When selecting a model, check the following points:

- **Format**: The file should end with `.gguf`.
- **Instruction tuning**: Prefer an `Instruct` or `Coder` model for Claw Code.
- **Quantization**: `Q4_K_M` is a common balanced choice for local usage.
- **Memory**: Larger models require more RAM or VRAM.

Example model families to search for on Hugging Face:

- Qwen Coder GGUF models
- DeepSeek Coder GGUF models
- Llama Instruct GGUF models
- Mistral or Mixtral Instruct GGUF models

For a first test, a 7B or 8B instruct/coder model with `Q4_K_M` quantization is usually easier to run than a larger model.

## 5. Download a model with huggingface-cli

Create a models directory:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\Documents\local-ai\models"
Set-Location "$env:USERPROFILE\Documents\local-ai\models"
```

Download a specific GGUF file:

```powershell
huggingface-cli download REPOSITORY_NAME FILE_NAME.gguf --local-dir .\MODEL_FOLDER
```

Replace the placeholders:

- `REPOSITORY_NAME`: Hugging Face repository name, for example `owner/model-repository`
- `FILE_NAME.gguf`: the GGUF file you want to download
- `MODEL_FOLDER`: local folder name

Example:

```powershell
huggingface-cli download bartowski/Qwen2.5-Coder-7B-Instruct-GGUF Qwen2.5-Coder-7B-Instruct-Q4_K_M.gguf --local-dir .\qwen2.5-coder-7b
```

After downloading, confirm the file exists:

```powershell
Get-ChildItem .\qwen2.5-coder-7b -Filter *.gguf
```

For convenience, you can rename the model file to `model.gguf`:

```powershell
Rename-Item .\qwen2.5-coder-7b\Qwen2.5-Coder-7B-Instruct-Q4_K_M.gguf model.gguf
```

## 6. Start llama-server.exe

Move to the llama.cpp directory:

```powershell
Set-Location "$env:USERPROFILE\Documents\local-ai\llama.cpp"
```

Start the server:

```powershell
.\llama-server.exe `
  -m "$env:USERPROFILE\Documents\local-ai\models\qwen2.5-coder-7b\model.gguf" `
  --host 127.0.0.1 `
  --port 8000 `
  --alias qwen2.5-coder-7b-local `
  -c 8192
```

Explanation:

- `-m`: path to the GGUF model file
- `--host 127.0.0.1`: listen only on your own PC
- `--port 8000`: expose the server on port 8000
- `--alias`: model name to use from OpenAI-compatible clients
- `-c 8192`: context size

Keep this PowerShell window open while using the local model.

!!! tip
    If your PC runs out of memory, use a smaller model, a stronger quantization such as `Q4_K_M` or `Q3_K_M`, or reduce the context size with `-c 4096`.

## 7. Test the local OpenAI-compatible endpoint

Open another PowerShell window and run:

```powershell
Invoke-RestMethod `
  -Uri "http://127.0.0.1:8000/v1/chat/completions" `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"model":"qwen2.5-coder-7b-local","messages":[{"role":"user","content":"Say hello in one sentence."}]}'
```

If the server is working, you should receive a response from the local model.

## 8. Connect Claw Code to llama-server.exe

In the PowerShell window where you want to run Claw Code, set OpenAI-compatible environment variables:

```powershell
$env:OPENAI_API_KEY = "local"
$env:OPENAI_BASE_URL = "http://127.0.0.1:8000/v1"
```

Then start Claw Code with the model alias you passed to `llama-server.exe`.

For a debug build:

```powershell
.\target\debug\claw.exe --model "qwen2.5-coder-7b-local"
```

For a release build:

```powershell
.\target\release\claw.exe --model "qwen2.5-coder-7b-local"
```

If `claw.exe` is already in your `Path`, you can run:

```powershell
claw --model "qwen2.5-coder-7b-local"
```

## Troubleshooting

### `llama-server.exe` is not found

Check where it was extracted or built:

```powershell
Get-ChildItem "$env:USERPROFILE\Documents\local-ai" -Recurse -Filter llama-server.exe
```

Use the actual folder that contains `llama-server.exe`.

### The model is too slow

Try the following:

- Use a smaller model.
- Use a more compact quantization.
- Reduce the context size with `-c 4096`.
- Use a GPU-enabled llama.cpp build if you have a compatible GPU.

### Claw Code cannot connect

Check that `llama-server.exe` is still running and that `OPENAI_BASE_URL` includes `/v1`:

```powershell
$env:OPENAI_BASE_URL
```

Expected value:

```txt
http://127.0.0.1:8000/v1
```

### Do not expose the server publicly by accident

For normal local usage, keep `--host 127.0.0.1`.

Avoid using `--host 0.0.0.0` unless you understand the security implications and have configured your firewall appropriately.
