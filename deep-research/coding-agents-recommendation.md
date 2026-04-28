# APIでモデルを自由に差し替えられるコーディングエージェントのランキング

## エグゼクティブサマリ

結論から言うと、**総合首位は Cline、次点は OpenCode**です。  
Cline は、MCP・Marketplace・Skills・タスク履歴/再開・チェックポイント・SDK・IDE/CLI の一体感が最も強く、機能の厚みとドキュメント整備で一歩抜けています。一方で、チェックポイント、ターミナル統合、MCP 互換、編集信頼性には大きめの既知課題があり、安定度は「非常に高い」ではなく「高いが荒れやすい箇所あり」です。OpenCode は、プロバイダ切替・任意の OpenAI 互換 API 追加・プラグイン・セッション/サーバー API・内蔵 websearch/webfetch まで揃っており、**端末中心・自作拡張重視なら最も柔軟**です。ただし Issue 量が非常に多く、特に compaction と OpenAI-compatible 周りはまだ荒いです。 

**新規導入の推奨順位**は、  
**Cline > OpenCode > Aider > Claw Code** です。  
ただし「必須評価軸の充実度」だけで見ると Roo Code も上位に入りますが、公式ドキュメントが **2026年5月15日に Extension / Cloud / Router を停止すると明言**しているため、**新規の本番導入先としては推奨しません**。Roo Code は“今この瞬間の機能面”だけならかなり強いものの、継続運用性で大きく減点です。Aider は逆に拡張面では弱い一方、CLI 型のペアプロ用途では依然かなり堅く、最小サプライズで使いやすいです。Claw Code は OpenRouter / OpenAI-compatible endpoint を使える完成度の高い芽が見えますが、まだ parity・MCP/plugin 周りの記述が散在し、成熟品というより「実験的に追う対象」です。 

## スコープと評価方法

今回のランキングは、**質問文中の「Claw-Code」は `ultraworkers/claw-code` と解釈**し、かつ**API 経由でモデル差し替えが明確に可能**なコーディングエージェントに絞りました。評価ソースは、公式ドキュメント、entity["company","OpenRouter","ai api gateway"] の接続ドキュメント、entity["company","GitHub","developer platform"] のリポジトリ/Issues を優先しています。特に「機能の有無」は公式 docs、「安定度」は公開 Issue の質と量、「差し替え容易さ」は OpenRouter または任意の entity["company","OpenAI","ai company"] 互換 endpoint の明示的サポートで見ました。 

スコアは **0〜5** で付与しました。  
**0 = 事実上なし、1 = 断片的/実験的、3 = 実用、5 = 強い実装** です。  
総合スコアは、**必須機能群の充実度を最重視**しつつ、そこに **安定度、ドキュメント品質、OpenRouter 互換性/モデル差し替え容易さ、実運用の痕跡** を加味した私の合成評価です。なお、**ドキュメントに明示がない機能は保守的に低め採点**しています。これはとくに Aider と Claw Code で効いています。

## ランキング

**凡例**: Market = Marketplace、Compact = auto-compaction、WSearch = websearch、WFetch = webfetch。

| 順位 | エージェント | 総合スコア | MCP | Plugin | Skills | Market | Resume | Sessions | Compact | Tools | WSearch | WFetch | Read | Edit | OpenRouter / 差し替え | 主要ソース短評 |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|---|
| 1 | Cline | **86/100** | 5 | 3 | 5 | 5 | 4 | 5 | 4 | 5 | 2 | 2 | 5 | 4 | **ネイティブ対応。OpenRouter を選ぶだけでモデル一覧を取得可能。** ただし Web Tools は docs 上 Cline provider 前提。 | Docs は最強クラス。Tasks/Resume/Checkpoints/Skills/MCP Marketplace が揃う。既知課題は checkpoints・task history・edit reliability・terminal・MCP。  |
| 2 | OpenCode | **84/100** | 5 | 5 | 5 | 2 | 5 | 5 | 4 | 5 | 5 | 5 | 5 | 5 | **非常に強い。75+ providers、任意 OpenAI-compatible provider を追加可能。** | provider/plugin/skills/MCP/tools/sessions/server API の幅は最広級。弱点は marketplace の薄さと compaction / model discovery 周りの荒さ、Issue 母数の多さ。  |
| 3 | Roo Code | **67/100** | 5 | 2 | 5 | 5 | 3 | 3 | 5 | 5 | 0 | 0 | 5 | 5 | **ネイティブ対応。OpenRouter と OpenAI Compatible を provider/profile で切替。** | Modes・Skills・Marketplace・Checkpoints・Context Condensing まで揃う機能量は多い。ただし **2026年5月15日で全製品停止予定**のため新規導入は非推奨。  |
| 4 | Aider | **56/100** | 0 | 0 | 0 | 0 | 2 | 2 | 0 | 3 | 0 | 4 | 5 | 5 | **強い。OpenRouter model slug をそのまま指定可能。** | CLI ペアプロとしては成熟。/ask・/architect・/web・--read は明確。逆に MCP / plugin / skills / marketplace は今回確認した一次資料では見当たらず、必須軸で大きく失点。  |
| 5 | Claw Code | **51/100** | 2 | 2 | 3 | 0 | 4 | 4 | 0 | 4 | 0 | 0 | 4 | 4 | **強い。OPENAI_BASE_URL + OPENAI_API_KEY で OpenRouter / 任意 OpenAI-compatible endpoint を利用可能。** | sessions/resume/skills/MCP command は見えるが、公式資料は repo markdown 中心で散在。ACP 未整備、auto-compaction の docs なし。**将来性はあるが現時点では未成熟**。  |

この順位で差がついた最大要因は、**Cline が「機能の広さ」と「ユーザー向けの完成度」を同時に持っていること**、そして **OpenCode が最も自由度の高い provider/plugin/server 系アーキテクチャを持つこと**です。Roo Code は機能表だけ見ると Cline/OpenCode にかなり近いのですが、公開 shutdown 通知によって運用上の安定性評価が大きく崩れます。Aider は「コーディング相棒」としては今も非常に優秀ですが、今回の必須評価軸では拡張機構が足りません。Claw Code は session/skills/plugin 的な志向が強い一方、一次資料の整い方と成熟度がまだ追いついていません。 

## 上位エージェント詳細

### Cline

Cline の強さは、**MCP・Skills・Marketplace・Sessions/Resume・Checkpoints がひとつの UX にまとまっていること**です。公式 docs では、Tasks を自己完結セッションとして扱い、履歴からの検索・再開ができ、各 tool 実行ごとに shadow Git ベースの checkpoint を作成し、MCP Marketplace からサーバーを導入でき、Skills は on-demand で読み込まれます。さらに SDK ではセッションを独立コンテキストとして扱えます。つまり、**「単にモデルを差し替えられる」だけでなく、長い作業の継続・回復・拡張が一番体系化されている**のが Cline です。 

必須機能軸で見ると、**MCP は最上位、Marketplace も最上位、Skills も最上位**です。Plugin は OpenCode のような「plugin を npm/local file で入れる」明示的 SDK ほどではありませんが、Rules / Skills / Workflows / Hooks / SDK という多層の拡張点があるため、実務では十分強いです。Read/Edit/Command/Browser は docs 上は非常に明確で、Plan & Act によって「読むだけ」と「変更する」を切り替えられるのも UX 上の完成度が高いです。 

ただし、**websearch と webfetch は caveat 付き**です。Cline docs の Web Tools は `web_search` と `web_fetch` を備えていますが、**利用条件として “Cline provider を使っていること” が明示**されています。したがって、**OpenRouter でモデルを自由差し替えする構成では、Web Tools の体験をそのまま持ち込めるとは限らない**、というのが大きな注意点です。この一点だけで、OpenCode に対する優位がかなり縮まります。 

実運用の痕跡はかなり濃いです。Cline docs は VS Code / JetBrains / CLI を明示し、enterprise deployment まで提供しています。GitHub 側でも 60k 超の stars があり、MCP Marketplace 専用 repo まで存在します。つまり、**単なる OSS 実験ではなく、IDE 埋め込み・組織導入・拡張エコシステムまで回り始めている**と見てよいです。 

既知の問題は軽くなく、主に **checkpoint / task history / file editing / terminal integration / MCP** に集中しています。公開 master issue では、checkpoint の shadow Git が大規模 repo で破損やディスク肥大を起こす、task history が壊れて resume できなくなる、replace/write 系 edit が diff mismatch でループする、terminal integration が shell/WSL/SSH 環境で不安定、MCP hub が secondary sidebar や remote server で壊れる、といった問題がまとめられています。回避策としては、**巨大 repo では checkpoints を切る、履歴が飛んだら recovery command を使う、MCP は primary sidebar 寄りの保守運用、モデル互換が怪しい provider では built-in tools を慎重に使う**、がまず現実的です。 

### OpenCode

OpenCode は、**“モデル差し替えの自由度” と “端末/サーバー中心の拡張性” では今回の調査対象の中で最も強い**です。公式 docs は AI SDK と models.dev を使って **75+ providers** をサポートすると明言し、さらに **任意の OpenAI-compatible provider** を `provider` 設定に追加できる構造を持っています。しかも baseURL・headers・apiKey・model map まで明示的に構成できるため、**OpenRouter だけでなく、社内 gateway・LM Studio・Ollama・llama.cpp 系に広く繋げやすい**です。 

必須機能軸との相性も非常に良いです。MCP は local/remote/OAuth まで整理され、Plugins は local file と npm package の両方に対応し、Skills は `SKILL.md` を repo / global / Claude-compatible / agent-compatible path から探索します。Tools は built-in と custom tools と MCP をひとつの権限モデルで扱い、そこに **websearch / webfetch / read / edit / bash / apply_patch / skill** まで揃っています。つまり OpenCode は、**「agent core に provider と extension を差し込む箱」として最も整っている**と言えます。 

session 系も強いです。CLI docs では `--continue`、`--session`、`--fork`、`session list`、`export`、`import`、`share`、`serve`、`web` が用意されており、**単一 TUI の対話履歴ではなく、セッションを継続・分岐・移送・共有・API 化する発想**が最初から入っています。Resume/Sessions の点数を高くしたのはこのためです。auto-compaction も config に `compaction.auto / prune / reserved` があり、機能としてははっきり実装されています。 

実運用の痕跡もかなり強いです。GitHub では 151k stars 規模、4.5k open issues、779 releases、最新リリース日も 2026年4月27日で、Desktop beta・IDE extension・headless server・web interface まで同時に回っています。これは「勢いは非常に強いが、荒削りな面も比例して大きい」タイプの OSS です。完成度は高いものの、**“安定して枯れている” とはまだ言いづらい**です。 

既知の問題は、**compaction 周りがいちばん気になる**ところです。Issue では、汎用 `/compact` や fork-aware compaction の要望、compaction 後に `opencode run` が終了するバグ、threshold を設定できない問題、auto-compaction が無限ループする問題、さらに OpenAI-compatible endpoint で model auto-discovery が弱い問題が公開されています。回避策としては、**OpenAI-compatible provider はモデルを明示定義する、compaction は `reserved` を大きめに取る／必要なら `auto` を切る、prune を活用する**のが現実的です。OpenCode は柔軟ですが、そのぶん**構成責任がユーザー側に寄る**ツールです。 

### Roo Code

Roo Code は、**機能の表面積だけならいまでもかなり強い**です。公式 docs には、OpenRouter / OpenAI Compatible / LiteLLM などの provider 群、API Configuration Profiles、Mode ごとの sticky model、Custom Modes、Skills、Roo Code Marketplace、Checkpoints、Intelligent Context Condensing、MCP、Orchestrator/ Boomerrang 系が一通り見えています。評価軸に沿って言い換えると、**MCP・skills・marketplace・resume 的な task persistence・auto-compaction 相当の context condensing・read/edit/tools** はほぼ揃っています。 

OpenRouter との相性も良いです。Model Providers docs は OpenRouter を明示し、Profile docs では provider / model / temperature / rate limit を profile 化でき、task は開始時 profile を覚えるとあります。Modes も last-used model を記憶するので、**Architect は大きいモデル、Code は安いモデル、みたいな mode 別 routing がやりやすい**のは大きな美点です。Cline よりこちらの方が、**“複数モデルを手元で切り回す” 体験は直感的**です。 

導入実績も無視できません。Docs のトップと installation page からは、GitHub 15.4k、VS Code Marketplace 574.1k installs が読み取れます。つまり Roo Code は単なる niche tool ではなく、**かなり広く使われてきた VS Code 系エージェント**です。 

ただし、ここで評価をひっくり返すのが **公式の shutdown 通知**です。Docs は、**Extension / Cloud / Router の全製品を 2026年5月15日に停止**し、代替として Cline や roomote.dev を案内しています。この一点で、**新規導入候補としての Roo Code は事実上脱落**です。機能点だけで 3 位に入れたのは正直な評価ですが、推奨順位では下げるべきです。 

既知の問題もあります。Issues では、OpenRouter 利用時の無応答ハング、custom model name が silently ignored され fallback する問題、diff editor / terminal / worktree lifecycle の不具合、さらに CLI 側の OpenAI-compatible endpoint サポートが enhancement request のまま残っています。したがって、**既存ユーザーの短期利用なら extension の provider/profile 運用に寄せる、CLI の OpenAI-compatible endpoint を前提に設計しない、Cloud/Router 依存を急いで外す**のが現実的です。 

## 導入シナリオ別の短い推奨

- **最初の本命を一つだけ選ぶなら Cline**。MCP・Marketplace・Skills・Resume・Checkpoints・IDE/CLI・Docs のバランスが最も良く、チーム導入の説明もしやすいです。Web Tools は provider 依存の caveat があるものの、総合完成度は最上位です。 
- **端末中心で、API 差し替え自由度と自作拡張を最重視するなら OpenCode**。任意 OpenAI-compatible provider、plugin、skills、MCP、headless server、websearch/webfetch まで揃っており、上級者向けの「箱」として非常に強いです。 
- **拡張性よりも CLI で堅実にコードを書きたいなら Aider**。MCP/marketplace は弱いですが、OpenRouter、read-only file 指定、ask/code/architect、URL 取り込みは明快で、道具としての理解コストが低いです。 
- **Roo Code は既存運用の撤収/移行対象**。新規採用は勧めません。特に Cloud / Router 前提のチームは、2026年5月15日以前に Cline か OpenCode へ移行設計を始めるべきです。 
- **Claw Code はラボ用途なら面白いが、本番の主力にはまだ早い**。OpenRouter や OpenAI-compatible endpoint に繋げやすい点は良いものの、docs の整い方・parity 状態・ACP 未整備を踏まえると、今は評価追跡枠です。 

## Open questions / limitations

今回の比較では、**公式 docs に機能が明示されていないものは保守的に採点**しました。そのため、Aider と Claw Code は「実際には何らかの実装があるが docs で一次確認しにくい」領域が残っています。逆に Cline と OpenCode は docs が非常に厚く、そのぶん評価しやすく、点数面でも有利です。

また、**“OpenRouter で使えること” と “その agent の全機能が OpenRouter 利用時にも同じように使えること” は別問題**です。今回もっとも重要な caveat は Cline の Web Tools で、docs 上は Cline provider 前提です。この種の provider 依存差は、今後のバージョンで変わる可能性があります。  
それでも、**現時点の高信頼な答えは「Cline が総合首位、OpenCode が拡張性首位、Aider が保守的CLI用途の安全牌、Roo Code は機能豊富だが新規導入非推奨、Claw Code は将来枠」**です。