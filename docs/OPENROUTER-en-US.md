# Using OpenRouter with Claw Code

This guide explains how to prepare OpenRouter for use with Claw Code.

OpenRouter provides an OpenAI-compatible API endpoint that can route requests to many different model providers. With Claw Code, you can use it by setting `OPENAI_API_KEY`, `OPENAI_BASE_URL`, and a model name.

## What you will do

1. Create an OpenRouter account.
2. Add paid credits with a credit card.
3. Create an API key.
4. Choose a model.
5. Start Claw Code with OpenRouter settings.

!!! warning
    API keys are secrets. Do not commit them to Git, paste them into public issues, or share screenshots that contain them.

## 1. Create an OpenRouter account

1. Open OpenRouter:
   - https://openrouter.ai/
2. Click **Sign In** or **Log In**.
3. Sign in with one of the available login methods.
4. After signing in, open the account or dashboard area.

If this is your first time using OpenRouter, check your account settings and confirm that your email or login method is correctly registered.

## 2. Add credits

OpenRouter uses account credits for paid model usage.

1. Open the OpenRouter page and hover over **Personal** in the upper-right corner.
2. Open **Credits**.
3. Select **Add Credits**.
4. Choose the amount you want to add.
5. Enter your address, billing name, and credit card information. If your payment details are already saved in Stripe, this step may be easier.
6. Pay by credit card. If the payment does not work, try **Use one-time payment methods**.
7. Confirm that the credit balance has increased after payment.

!!! note
    The exact labels on the OpenRouter website may change. Look for pages related to credits, billing, account balance, or payments.

!!! tip
    For an initial test, start with a small amount of credits. After confirming that Claw Code works with your chosen model, add more if needed.

## 3. Create an API key

1. Click **Personal** in the upper-right corner.
2. Open **API Keys** from the left sidebar.
3. Click **Create** to create a new API key. Give it a descriptive name so that you can identify its purpose later.
4. Copy the generated key.
5. Store it in a secure place, such as a password manager.

You will use this key as `OPENAI_API_KEY`.

!!! warning
    Depending on the service UI, the full API key may be shown only once. Copy it immediately and store it safely.

## 4. Choose a model

OpenRouter supports many models. For Claw Code, prefer models that are strong at coding, instruction following, and tool-use style workflows.

### What to check when choosing a model

Check the following points on the OpenRouter model page:

- **Model name / slug**: This is the value passed to `--model`.
- **Context length**: Larger context is useful for reading code and long files.
- **Pricing**: Check input and output token costs.
- **Provider availability**: Some models may be served by multiple providers.
- **Tool/function support**: Useful for coding-agent workflows if available.
- **Latency and throughput**: Faster models are more comfortable for interactive work.
- **Quality for coding**: Prefer models known for code generation and debugging.

### Common model categories

For coding tasks, consider these categories:

- Frontier general-purpose models with strong coding ability.
- Coding-specialized models.
- Fast and low-cost models for small edits or quick questions.
- Large-context models for reading bigger repositories.

### Example model names

Model availability and names can change, so always confirm the current model slug on OpenRouter before using it.

Examples of the kind of model names you may see:

```txt
anthropic/claude-opus-4.7
openai/gpt-5.5
openai/gpt-5.4-mini
google/gemini-3.1-pro-preview
qwen/qwen3.6-plus
x-ai/grok-4.20
```

If a model fails to start, re-check the exact model slug on OpenRouter and confirm that your account has enough credits.

## 5. Set environment variables for the current terminal

This is the safer method for testing because the key disappears when the terminal is closed.

Open PowerShell and run:

```powershell
$env:OPENAI_API_KEY = "YOUR_OPENROUTER_API_KEY"
$env:OPENAI_BASE_URL = "https://openrouter.ai/api/v1"
```

Replace `YOUR_OPENROUTER_API_KEY` with your actual OpenRouter API key.

## 6. Start Claw Code

Move to the Claw Code `rust` directory if you are running the executable from the build output directory.

Then start Claw Code with an OpenRouter model name.

For a debug build:

```powershell
.\target\debug\claw.exe --model "MODEL_NAME_ON_OPENROUTER"
```

For a release build:

```powershell
.\target\release\claw.exe --model "MODEL_NAME_ON_OPENROUTER"
```

Example:

```powershell
.\target\release\claw.exe --model "anthropic/claude-opus-4.7"
```

If `claw.exe` is already in your `Path`, you can run:

```powershell
claw --model "anthropic/claude-opus-4.7"
```

## 7. Save settings permanently, if needed

If you use OpenRouter regularly, you can save the environment variables to your user profile.

```powershell
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "YOUR_OPENROUTER_API_KEY", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://openrouter.ai/api/v1", "User")
```

After setting them permanently, open a new PowerShell window.

!!! warning
    Permanent environment variables are convenient, but they store your API key in your Windows user environment. Use this only on a trusted PC.

## 8. Control spending

To avoid unexpected spending:

- Start with a small amount of credits.
- Check token pricing before selecting a model.
- Prefer cheaper models for simple tasks.
- Use stronger models only when the task needs them.
- Check your OpenRouter usage history regularly.
- Stop Claw Code when you are not using it.

## Troubleshooting

### Authentication error

Check the following:

- `OPENAI_API_KEY` is set to your OpenRouter API key.
- The key was copied correctly.
- The key has not been deleted or disabled.

In PowerShell, you can check whether the variable is set without printing the secret:

```powershell
if ($env:OPENAI_API_KEY) { "OPENAI_API_KEY is set" } else { "OPENAI_API_KEY is not set" }
```

### Model not found

The model slug may be incorrect or outdated.

1. Open the OpenRouter model page.
2. Copy the exact model slug.
3. Pass that value to `--model`.

### Insufficient credits

Add credits from the OpenRouter dashboard, or choose a cheaper/free model if available.

### Wrong API endpoint

Confirm that `OPENAI_BASE_URL` is set to:

```txt
https://openrouter.ai/api/v1
```

Do not add `/chat/completions` to `OPENAI_BASE_URL`; Claw Code adds the API path internally.
