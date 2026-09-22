# fizz-llm-client

**Fizz** (AITuber system in [Almide](https://github.com/almide/almide)) — §3 brain 部品。

マルチプロバイダ LLM クライアント (Anthropic / OpenAI / OpenRouter / Groq)。
`Message` / `LLMResponse` / provider 解析 / body 構築 / レスポンス解析。

## API

| 関数 | 説明 |
|---|---|
| `call(model_spec, msgs)` / `call_with(.., opts)` | 非ストリーミング。全文を待って `LLMResponse` を返す |
| `call_streaming(model_spec, msgs, on_delta)` / `call_streaming_with(.., opts, on_delta)` | **ストリーミング**。本文差分が届くたび `on_delta(String)` を呼び、終了後に蓄積済み `LLMResponse` を返す |

ストリーミングは下流 (文分割 → TTS → アバター発話) が生成完了を待たず**最初の文から
喋り出せる**ので first-audio latency を縮める。`model_spec` は `anthropic/claude-...`,
`openai/gpt-...`, `openrouter/...`, `groq/...` の `provider/model` 形式。

```almide
let resp = fizz_llm_client.call_streaming(
  "anthropic/claude-haiku-4-5",
  [fizz_llm_client.user("こんにちは")],
  (delta) => { print(delta) },   // トークンが届くたび
)!
```

責務は一行で、入力 → 出力が型で言い切れる単位 (openaituber `docs/almide-component-breakdown.md` §3)。

## Install

```toml
[dependencies]
fizz_llm_client = { git = "https://github.com/aiviecast/fizz-llm-client", tag = "v0.2.0" }
```

## Tests

```bash
almide test
```

純ロジックなのでネットワーク・API キー不要でテストできる。
