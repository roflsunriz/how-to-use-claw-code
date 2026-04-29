# Claw CodeでOpenRouterを使う

このページでは、Claw CodeからOpenRouterを利用するための準備手順を説明します。

OpenRouterは、複数のモデルプロバイダーをOpenAI互換APIとして利用できるサービスです。Claw Codeでは、`OPENAI_API_KEY`、`OPENAI_BASE_URL`、モデル名を設定することで利用できます。

## このページで行うこと

1. OpenRouterのアカウントを作成する。
2. 有料モデルを使う場合は、クレジットカード決済でクレジットを購入する。
3. APIキーを作成する。
4. 無料モデルも含めて、使用するモデルを選ぶ。
5. OpenRouter設定でClaw Codeを起動する。

!!! warning
    APIキーは秘密情報です。Gitへコミットしたり、公開Issueへ貼り付けたり、APIキーが写ったスクリーンショットを共有したりしないでください。

## 1. OpenRouterのアカウントを作成する

1. OpenRouterを開きます。
    - [OpenRouter](https://openrouter.ai/)
2. **Sign In**または**Log In**をクリックします。
3. 利用可能なログイン方法を選んでログインします。
4. ログイン後、アカウントまたはダッシュボードを開きます。

初めてOpenRouterを使う場合は、アカウント設定を確認し、メールアドレスまたはログイン方法が正しく登録されていることを確認してください。

## 2. クレジットを購入する

OpenRouterでは、有料モデルの利用にアカウントクレジットを使います。無料モデルが提供されている場合もあり、無料モデルだけを使う場合はクレジット購入は不要です。ただし、無料モデルは有料モデルよりもレート制限や混雑の影響を受けやすく、実際には`429`エラーでリクエストを拒否されることが多くあります。また、無料モデルの提供状況やモデル一覧は頻繁に入れ替わることがあるため、利用前にOpenRouter上で現在の状態を確認してください。

1. OpenRouterのページを開いたら、右上の**Personal**へマウスカーソルを合わせます。
2. **Credits**を開きます。
3. **Add Credits**を選びます。
4. 追加したい金額を選びます。
5. 住所、名前（決済名義）、クレジットカード情報を入力します。Stripeに情報を保存済みの場合は、より簡単に入力できます。
6. クレジットカードで支払います。うまくいかない場合は、**Use one-time payment methods**から決済します。
7. 支払い後、クレジット残高が増えていることを確認します。

!!! note
    OpenRouterの画面上の文言は変更されることがあります。クレジット、請求、残高、支払いに関係するページを探してください。

!!! tip
    有料モデルを試す場合は、最初は少額のクレジットから始めるのがおすすめです。Claw Codeと選んだモデルで動作確認できてから、必要に応じて追加購入してください。

!!! note
    無料モデルだけを使う場合は、クレジット購入を省略できます。ただし、無料モデルは対話的なコーディングエージェント用途では不安定になることがあります。レート制限により`429`エラーが返る、応答が途切れる、利用可能なモデル一覧から消える、といった可能性があります。

## 3. APIキーを作成する

1. 画面右上の**Personal**をクリックします。
2. 左のサイドバーにある**API Keys**ページを開きます。
3. **Create**ボタンを押して、新しいAPIキーを作成します。用途が分かる名前を付けておくと管理しやすくなります。
4. 生成されたキーをコピーします。
5. パスワードマネージャーなど、安全な場所へ保存します。

このキーを`OPENAI_API_KEY`として使用します。

!!! warning
    サービスのUIによっては、APIキー全体が一度しか表示されないことがあります。作成直後にコピーして、安全に保存してください。

## 4. モデルを選ぶ

OpenRouterでは多くのモデルを利用できます。Claw Code用途では、コーディング、指示追従、エージェント的な作業に強いモデルを選ぶと扱いやすいです。

### モデル選定時に確認すること

OpenRouterのモデルページで、次の点を確認します。

- **モデル名 / slug**: `--model`に渡す値です。
- **コンテキスト長**: コードや長いファイルを読む場合は大きいほど便利です。
- **価格**: 入力トークン、出力トークンの料金を確認します。無料モデルが提供されている場合もありますが、レート制限や不安定さに注意してください。
- **プロバイダーの可用性**: 同じモデルが複数プロバイダーから提供される場合があります。
- **ツール / function対応**: コーディングエージェント的な用途では対応していると便利です。
- **レイテンシとスループット**: 応答が速いモデルほど対話的に使いやすいです。
- **コーディング品質**: コード生成やデバッグに強いモデルを優先します。

### モデルの大まかな選び方

コーディング用途では、次のカテゴリを検討します。

- コーディング能力の高いフロンティア系汎用モデル。
- コード特化モデル。
- 小さな修正や質問に向いた高速・低価格モデル。
- 大きめのリポジトリを読むための長コンテキストモデル。

### おすすめのモデル選定

Claw Codeでは次の構成が実用的です。

- **総合的におすすめの split 構成**
  - 最も難しいタスク: `anthropic/claude-opus-4.6` / `openai/gpt-5.5-pro`
  - 難しいタスク: `anthropic/claude-sonnet-4.6` / `openai/gpt-5.4`
  - 簡単〜中程度のタスク: `google/gemini-3-flash-preview` / `openai/gpt-5-mini`
- **最安重視の split 構成**
  - 最も難しいタスク: `x-ai/grok-4.20-multi-agent`
  - 難しいタスク: `deepseek/deepseek-v4-pro`
  - 簡単タスク: `deepseek/deepseek-v4-flash`
- **単一モデルで使うなら**: `google/gemini-3-flash-preview`
- **難しいタスクを安全側で回すなら**: `anthropic/claude-sonnet-4.6`

迷ったら、まずは `google/gemini-3-flash-preview` から始めるのがおすすめです。コーディング品質、長コンテキスト、速度、コストのバランスが良いからです。複雑なリファクタリング、原因調査、複数ファイルにまたがるデバッグが多いなら、`anthropic/claude-sonnet-4.6` のほうが扱いやすいです。コスト効率を最優先するなら DeepSeek の split 構成が最強ですが、保守的な運用や機微なコードでは少し攻めた選択になります。

### モデル名の例

モデルの提供状況や名前は変わる可能性があるため、利用前にOpenRouter上で現在の正確なモデルslugを確認してください。

表示される可能性があるモデル名の例:

```txt
anthropic/claude-opus-4.6
openai/gpt-5.5-pro
anthropic/claude-sonnet-4.6
google/gemini-3-flash-preview
deepseek/deepseek-v4-pro
deepseek/deepseek-v4-flash
openai/gpt-5.5
openai/gpt-5.4
openai/gpt-5.4-mini
openai/gpt-5-mini
google/gemini-3.1-pro-preview
qwen/qwen3.6-plus
moonshotai/kimi-k2.6
minimax/minimax-m2.7
z-ai/glm-5.1
x-ai/grok-code-fast-1
```

モデル起動に失敗する場合は、OpenRouterで正確なモデルslugを確認し、有料モデルの場合はアカウントに十分なクレジットがあるかも確認してください。無料モデルの場合は、モデルが現在も提供されているか、`429 Too Many Requests`のようなレート制限で拒否されていないかも確認してください。

## 5. 現在のターミナルだけに環境変数を設定する

動作確認ではこの方法が安全です。ターミナルを閉じるとAPIキーは消えます。

PowerShellで次を実行します。

```powershell
$env:OPENAI_API_KEY = "YOUR_OPENROUTER_API_KEY"
$env:OPENAI_BASE_URL = "https://openrouter.ai/api/v1"
```

`YOUR_OPENROUTER_API_KEY`は、実際のOpenRouter APIキーに置き換えてください。

## 6. Claw Codeを起動する

ビルド出力先から実行する場合は、Claw Codeの`rust`ディレクトリへ移動します。

たとえば、リポジトリを`Documents`にクローンした場合は次のようにします。

```powershell
Set-Location "$env:USERPROFILE\Documents\claw-code\rust"
```

または、リポジトリの親フォルダにいる場合は相対パスで移動できます。

```powershell
Set-Location .\claw-code\rust
```

その後、OpenRouterのモデル名を指定してClaw Codeを起動します。

Debugビルドの場合:

```powershell
.\target\debug\claw.exe --model "MODEL_NAME_ON_OPENROUTER"
```

Releaseビルドの場合:

```powershell
.\target\release\claw.exe --model "MODEL_NAME_ON_OPENROUTER"
```

例:

```powershell
.\target\release\claw.exe --model "anthropic/claude-opus-4.7"
```

すでに`claw.exe`へ`Path`を通している場合は、次のように実行できます。

```powershell
claw --model "anthropic/claude-opus-4.7"
```

## 7. 必要に応じて設定を永続化する

OpenRouterを日常的に使う場合は、環境変数をユーザー環境変数として保存できます。

```powershell
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "YOUR_OPENROUTER_API_KEY", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://openrouter.ai/api/v1", "User")
```

設定後、新しいPowerShellウィンドウを開いてください。

!!! warning
    永続化は便利ですが、APIキーをWindowsのユーザー環境変数に保存します。信頼できるPCでのみ使用してください。

## 8. 利用料金を管理する

想定外の出費を避けるため、次を意識してください。

- 最初は少額のクレジットから始める。
- モデルを選ぶ前にトークン料金を確認する。
- 簡単な作業には安価なモデルを使う。
- 難しい作業にだけ高性能モデルを使う。
- OpenRouterの利用履歴を定期的に確認する。
- 使っていないときはClaw Codeを終了する。

## トラブルシューティング

### 認証エラーになる

次を確認してください。

- `OPENAI_API_KEY`にOpenRouterのAPIキーを設定している。
- APIキーを正しくコピーしている。
- APIキーを削除または無効化していない。

PowerShellでは、秘密情報を表示せずに環境変数が設定されているか確認できます。

```powershell
if ($env:OPENAI_API_KEY) { "OPENAI_API_KEY is set" } else { "OPENAI_API_KEY is not set" }
```

### モデルが見つからない

モデルslugが間違っているか、古くなっている可能性があります。

1. [OpenRouterのモデル](https://openrouter.ai/models)ページを開きます。
2. 正確なモデルslugをコピーします。
3. その値を`--model`に渡します。

### クレジット不足になる

OpenRouterのダッシュボードからクレジットを追加するか、利用可能であれば安価なモデルや無料モデルを選んでください。

### APIエンドポイントが間違っている

`OPENAI_BASE_URL`が次の値になっていることを確認します。

```txt
https://openrouter.ai/api/v1
```

`OPENAI_BASE_URL`に`/chat/completions`を追加しないでください。Claw Code側が内部でAPIパスを追加します。
