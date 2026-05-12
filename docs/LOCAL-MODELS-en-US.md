# Local Models with llama.cpp on Windows

This guide explains how to run a local model with `llama.cpp` and connect Claw Code to it through an OpenAI-compatible local endpoint.

The goal is to run the following flow:

1. Install or obtain `llama.cpp`.
2. Install CUDA if you use an NVIDIA GPU.
3. Install Python.
4. Install `hf` and download a GGUF model from Hugging Face.
5. Start `llama-server.exe` locally.
6. Start Claw Code with `OPENAI_BASE_URL` pointing to the local server.

!!! note
    Local models do not require paid API credits, but they require enough CPU, RAM, and preferably a powerful GPU with plenty of VRAM. Larger models are slower and require more memory.

!!! warning
    Very small local models may not work well for coding-agent workflows. They can produce broken or collapsed output, fail to use the terminal appropriately in an agentic workflow, or hallucinate commands, files, APIs, and results. Use an instruction-tuned or coder-oriented model that is large enough for the task whenever possible.

## Recommended folder layout

This guide uses the following folders:

```txt
%USERPROFILE%\Documents\local-ai\
├── llama.cpp\
│   └── llama-server.exe
└── models\
    └── devstral-small-2507-q4km\
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
    - [llama.cpp releases](https://github.com/ggml-org/llama.cpp/releases)
2. Download a Windows build that matches your environment.
    - For CPU-only usage, choose a Windows archive that includes `llama-server.exe`. The file name may look like `llama-(random string)-bin-win-cpu-x64.zip`.
    - For NVIDIA GPU inference, choose a CUDA-enabled Windows archive and install the matching CUDA runtime. The file name may look like `cudart-llama-bin-win-cuda-12.4-x64.zip`, `cudart-llama-bin-win-cuda-13.1-x64.zip`, `llama-(random string)-bin-win-cuda-12.4-x64.zip`, or `llama-(random string)-bin-win-cuda-13.1-x64.zip`.
    - If you want to use a different GPU backend, `llama.cpp` also ships Windows builds for Vulkan, SYCL, and HIP.
3. Extract the archive.
4. Copy or move the extracted folder to:

    ```txt
    %USERPROFILE%\Documents\local-ai\llama.cpp
    ```

5. Download the `cudart-llama-bin-win-cuda-<version>-x64.zip` archive, then extract its DLL files into the `llama.cpp` folder next to `llama-server.exe`.
6. Confirm that `llama-server.exe` exists:

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

```powershell
Get-ChildItem -Recurse -Filter llama-server.exe
```

## 2. Install CUDA if you use NVIDIA GPU inference

If you use an NVIDIA GPU-enabled `llama.cpp` build, CUDA is required in addition to an NVIDIA GPU driver. For Vulkan, SYCL, or HIP builds, skip CUDA and use the matching backend archive instead.

First, check the available CUDA versions:

```powershell
winget show Nvidia.CUDA --versions
```

### Installing CUDA 13

If CUDA 13 is acceptable for your build, install it with:

```powershell
winget install Nvidia.CUDA --source winget
```

!!! warning
    Running `winget install Nvidia.CUDA` installs the latest CUDA version published through winget. At the time of writing, this may install CUDA 13.2. If you need CUDA 12, do not run this command as-is; specify a CUDA 12 version as shown below.

### Installing CUDA 12

If you are using a CUDA 12 `llama.cpp` archive, specify a CUDA 12 version shown by `winget show Nvidia.CUDA --versions`.

```powershell
winget install Nvidia.CUDA --source winget --version 12.9
```

After installation, restart Windows Terminal and check the installed version:

```powershell
nvcc --version
```

!!! tip
    If a prebuilt archive file name contains a label such as `cuda-12.4` or `cuda-13.1`, use a matching CUDA major version whenever possible. Pair CUDA 12 archives with CUDA 12, and CUDA 13 archives with CUDA 13 to avoid runtime issues.

## 3. Install Python

`hf` is distributed as a Python package, so install Python before installing `huggingface_hub`.

Install Python with winget:

```powershell
winget install Python.Python.3.12 --source winget
```

After installation, close and reopen Windows Terminal. Then check that Python and pip are available:

```powershell
python --version
python -m pip --version
```

If `python` is not found, restart Windows Terminal again. If it is still not found, check that Python was added to your `Path`.

!!! warning
    Do not install the latest Python version. Some required packages may not have adopted the latest Python yet, which can cause dependency resolution failures and prevent installation.

## 4. Install hf

`hf` is included in the `huggingface_hub` Python package.

```powershell
python -m pip install -U huggingface_hub
```

Check that the command is available:

```powershell
hf --help
```

If `hf` is not found, close and reopen Windows Terminal, then try again. If it is still not found, check that Python's `Scripts` directory was added to your `Path`.

## 5. Log in to Hugging Face when needed

Some models require accepting a license or logging in before download.

1. Create or open your Hugging Face account.
2. If the model page requires accepting terms, accept them in the browser.
3. Create an access token on Hugging Face.
4. Log in from PowerShell:

```powershell
hf login
```

Paste your token when prompted.

!!! warning
    Do not paste your Hugging Face token into public repositories, screenshots, or shared logs.

## 6. Choose a GGUF model

For `llama.cpp`, choose a model in the GGUF format.

When selecting a model, check the following points:

- **Format**: The file should end with `.gguf`.
- **Instruction tuning**: Prefer an `Instruct` or `Coder` model for Claw Code.
- **Quantization**: `Q4_K_M` is a common balanced choice for local usage. Lower quantization numbers generally use less memory. Q8 uses more memory, while Q4 uses less.
- **Memory**: Larger models require more RAM or VRAM. Model sizes are commonly written as 27B, 35B, 500M, and similar labels.

As of 2026/04, do not guess. Only list models that have a real GGUF repository and whose selected quantization fits the target VRAM size with some headroom for the KV cache and runtime overhead.

The current research-backed starting points are `Devstral-Small-2507`, `Qwen3-Coder-30B-A3B-Instruct`, `Qwen2.5-Coder-14B-Instruct`, `Qwen2.5-Coder-7B-Instruct`, and `DeepSeek-Coder-V2-Lite-Instruct` when you care more about long-context efficiency than a simple dense-model fit.

Recommended starting points by GPU memory:

| VRAM | Model | GGUF quant | Fit / context | Notes |
| --- | --- | --- | --- | --- |
| 32 GB | `mistralai/Devstral-Small-2507_gguf` | `Q5_K_M` | 128k native / around 90k usable headroom | Best overall agentic-coding choice in the report. |
| 32 GB | `unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF` | `Q4_K_M` | 262k native / around 100k+ practical headroom | Strong latest-style option for long-context tool use. |
| 24 GB | `mistralai/Devstral-Small-2507_gguf` | `Q4_K_M` | 128k native / around 50k+ headroom | Conservative fit with the highest confidence. |
| 24 GB | `unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF` | `Q4_K_M` | 262k native / around 30k+ headroom | Good if you want a MoE coder with a very long context. |
| 16 GB | `Qwen/Qwen2.5-Coder-14B-Instruct-GGUF` | `Q4_K_M` | Full 32,768 tokens / roughly 24k-30k practical | Strong dense coder and the safest 16 GB recommendation. |
| 16 GB | `DeepSeek-Coder-V2-Lite-Instruct` | `Q4_K_M` or `Q6_K` | Long-context specialist / runtime-dependent under GGUF | Worth testing if long-context efficiency matters more than a simple VRAM fit. |
| 12 GB | `Qwen/Qwen2.5-Coder-7B-Instruct-GGUF` | `Q4_K_M` | Full 32,768 tokens | Most reliable 12 GB option. |
| 12 GB | `DeepSeek-Coder-V2-Lite-Instruct` | `Q4_K_M` | Long-context specialist / runtime-dependent under GGUF | Good fallback for long documents and repo work. |
| 8 GB | `Qwen/Qwen2.5-Coder-7B-Instruct-GGUF` | `Q4_K_M` | Full 32,768 tokens | Safest 8 GB recommendation. |

If a model does not fit entirely in VRAM, `llama.cpp` can spill layers to system RAM, but this guide does not recommend that mode for the main examples because it is slower and the fit becomes harder to reason about.

Example search keywords for Hugging Face:

- `Devstral-Small-2507 GGUF`
- `Qwen3-Coder-30B-A3B-Instruct GGUF`
- `DeepSeek-Coder-V2-Lite-Instruct GGUF`
- `Qwen2.5-Coder-14B-Instruct GGUF`
- `Qwen2.5-Coder-7B-Instruct GGUF`
- `Qwen2.5-Coder-32B-Instruct GGUF`

For a first test, `Qwen2.5-Coder-7B-Instruct-GGUF` with `Q4_K_M` quantization is usually the easiest safe option. If Claw Code repeatedly produces malformed output, invents nonexistent commands, or cannot proceed with terminal-based work, move up to `Qwen2.5-Coder-14B-Instruct-GGUF` or `Devstral-Small-2507`.

In my tests, `unsloth/gpt-oss-20b-Q8_0.gguf` handled terminal-based workflows best.

## 7. Download a model with hf

Create a models directory:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\Documents\local-ai\models"
Set-Location "$env:USERPROFILE\Documents\local-ai\models"
```

Download a specific GGUF file:

```powershell
hf download REPOSITORY_NAME FILE_NAME.gguf --local-dir .\MODEL_FOLDER
```

Replace the placeholders:

- `REPOSITORY_NAME`: Hugging Face repository name, for example `owner/model-repository`
- `FILE_NAME.gguf`: the GGUF file you want to download
- `MODEL_FOLDER`: local folder name

Example:

```powershell
hf download mistralai/Devstral-Small-2507_gguf Devstral-Small-2507-Q4_K_M.gguf --local-dir .\devstral-small-2507-q4km
```

After downloading, confirm the file exists:

```powershell
Get-ChildItem .\devstral-small-2507-q4km -Filter *.gguf
```

For convenience, you can rename the model file to `model.gguf`:

```powershell
Rename-Item .\devstral-small-2507-q4km\Devstral-Small-2507-Q4_K_M.gguf model.gguf
```

## 8. Understand the llama.cpp options

The options below are the common `llama-server.exe` tuning knobs you are most likely to adjust. Start from a known-good baseline, then change the values if the model is too random, too repetitive, too slow, or too large for your GPU.

| Option | What it controls | Practical guidance |
| --- | --- | --- |
| `--temp` | Randomness of the output | Lower values are more deterministic. `0` is fully deterministic, while values around `0.7` to `0.8` are common for chat-style use. |
| `--top-p` | Nucleus sampling cutoff | Lower values make the output more conservative. `1.0` disables the filter; values around `0.9` to `0.95` are common. |
| `--top-k` | Limits the number of candidate tokens | Lower values reduce variety and can make output more focused. `0` disables top-k filtering. |
| `--presence-penalty` | Penalizes tokens that have already appeared | Use this to reduce repeated topics or phrases. Keep it at `0` if you do not need extra variety. |
| `--repeat-penalty` | Penalizes recently repeated tokens | Values slightly above `1.0`, such as `1.05`, can reduce looping or repeated wording. |
| `--ctx-size` | Context window size | Larger values let the model read more conversation and files, but use more VRAM or RAM. |
| `--n-gpu-layers` | Number of layers offloaded to the GPU | Use `all`, `auto`, or a numeric value. Increase it if you have more VRAM available. |
| `--n-cpu-moe` | Number of Mixture-of-Experts layers kept on CPU | Only relevant for MoE models. Change it if your build or model benefits from keeping some MoE work on CPU. |
| `--jinja` / `--no-jinja` | Enables or disables the Jinja chat template engine | Keep Jinja enabled for most chat models unless the model requires a custom prompt format. |
| `--cache-type-k` | KV cache data type | Lower-precision cache types reduce memory use. `f16` is the safest default, while `q4_0` or `q4_1` can save memory. |
| `--sleep-idle-seconds` | Automatically unloads the model after inactivity | Set `-1` to disable. Use a positive value if you want memory freed after the server sits idle. |

These options are not all required for every model. Tune the ones that match the problem you are solving.

## 9. Start llama-server.exe

Move to the llama.cpp directory:

```powershell
Set-Location "$env:USERPROFILE\Documents\local-ai\llama.cpp"
```

Start the server:

```powershell
.\llama-server.exe `
  -m "$env:USERPROFILE\Documents\local-ai\models\devstral-small-2507-q4km\model.gguf" `
  --host 127.0.0.1 `
  --port 8000 `
  --alias devstral-small-2507-q4km-local `
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
    If your PC runs out of memory, use a smaller model, a lighter quantization such as `Q4_K_M` or `Q3_K_M`, or reduce the context size with `-c 4096`.

## 10. Test the local OpenAI-compatible endpoint

Open another PowerShell window and run:

```powershell
Invoke-RestMethod `
  -Uri "http://127.0.0.1:8000/v1/chat/completions" `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"model":"devstral-small-2507-q4km-local","messages":[{"role":"user","content":"Say hello in one sentence."}]}'
```

If the server is working, you should receive a response from the local model.

## 11. Connect Claw Code to llama-server.exe

In the PowerShell window where you want to run Claw Code, set OpenAI-compatible environment variables:

```powershell
$env:OPENAI_API_KEY = "local"
$env:OPENAI_BASE_URL = "http://127.0.0.1:8000/v1"
```

If you are not already in the Claw Code `rust` directory, change to it first. For example:

```powershell
Set-Location "$env:USERPROFILE\Documents\claw-code\rust"
```

Or, if you are in the repository parent folder, use a relative path:

```powershell
Set-Location .\claw-code\rust
```

Then start Claw Code with the model alias you passed to `llama-server.exe`. The provider name is used to identify the API type, so add the `openai/` prefix to the model name.

For a debug build:

```powershell
.\target\debug\claw.exe --model "openai/devstral-small-2507-q4km-local"
```

For a release build:

```powershell
.\target\release\claw.exe --model "openai/devstral-small-2507-q4km-local"
```

If `claw.exe` is already in your `Path`, you can run:

```powershell
claw --model "openai/devstral-small-2507-q4km-local"
```

## Troubleshooting

### `llama-server.exe` is not found

Check where it was extracted or built:

```powershell
Get-ChildItem "$env:USERPROFILE\Documents\local-ai" -Recurse -Filter llama-server.exe
```

Use the actual folder that contains `llama-server.exe`.

Confirm that the CUDA runtime DLLs from your downloaded `cudart-llama-bin-win-cuda-<version>-x64.zip` package were extracted into the same `llama.cpp` folder as `llama-server.exe`.

### CUDA is installed, but the GPU is not used

Check the following:

- The NVIDIA driver is installed.
- You are using a CUDA-enabled `llama.cpp` archive, not a CPU-only, Vulkan, SYCL, or HIP build.
- CUDA 12 archives are paired with CUDA 12, and CUDA 13 archives are paired with CUDA 13.
- You restarted Windows Terminal and checked `nvcc --version`.

If the CUDA build starts but fails immediately, re-check that the DLL files from the `cudart-llama-bin-win-cuda-<version>-x64.zip` archive were extracted into the `llama.cpp` folder.

### The model is too slow

Try the following:

- Use a smaller model.
- Use a lighter quantization.
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
