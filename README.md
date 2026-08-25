# Model Mesh

> A focused, configuration-first gateway for routing requests to the AI models your application already uses.

Model Mesh is a project by **Kartik Bobde**. It provides one OpenAI-compatible API surface in front of a small, intentional set of model providers, so applications can route requests consistently without being tightly coupled to each provider's API.

## What Model Mesh covers

Model Mesh is deliberately limited in scope. It focuses on the practical gateway layer needed to connect an application to selected LLM providers:

- OpenAI-compatible request and response handling for chat completions, Responses, models, embeddings, image generation, and selected audio endpoints.
- Provider configuration, model aliases, API-key management, and per-model access rules.
- Resilient routing with retries, key rotation, rate limits, timeouts, health checks, and configurable load balancing.
- Streaming support, request observability, and runtime health information.
- A small configuration-first deployment model—no required frontend or control panel.

## What it does not aim to be

Model Mesh is not a hosted AI service or a replacement for your provider accounts. In its current scope, it does **not** provide:

- Model training, fine-tuning, or hosting.
- A consumer chat product, user workspace, or full admin dashboard.
- Billing, subscriptions, usage invoicing, or reseller functionality.
- A guarantee that every endpoint or feature of every upstream provider is supported.
- A place to store real credentials in source control.

Bring your own provider accounts and API keys. Model Mesh forwards requests to the providers you configure and keeps the integration layer consistent for your applications.

## Quick start

### 1. Create a local configuration

Create an `api.yaml` file outside source control. Start with this minimal example, replacing the placeholder values with your own:

```yaml
providers:
  - provider: openai
    base_url: https://api.openai.com/v1/chat/completions
    api: ${OPENAI_API_KEY}
    model:
      - gpt-4.1-mini

api_keys:
  - api: ${MODEL_MESH_API_KEY}
```

### 2. Run locally

Model Mesh requires Python 3.11 or later.

```bash
uv sync
uv run python main.py
```

The service listens on `http://localhost:8000` by default. Set `PORT` or `HOST` if you need different values.

### 3. Send a request

```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Authorization: Bearer $MODEL_MESH_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4.1-mini",
    "messages": [{"role": "user", "content": "Hello from Model Mesh"}]
  }'
```

## Supported integration surface

The gateway is designed around OpenAI-compatible clients. The primary endpoints currently in scope are:

| Capability | Endpoint |
| --- | --- |
| Chat completions | `/v1/chat/completions` |
| Responses | `/v1/responses` |
| Models | `/v1/models` |
| Embeddings | `/v1/embeddings` |
| Images | `/v1/images/generations` |
| Audio | `/v1/audio/speech`, `/v1/audio/transcriptions` |
| Health check | `/healthz` |

Provider support is configuration-driven. Use only the providers, models, and features your deployment requires; unavailable upstream features should be treated as out of scope.

## Configuration and safety

- Keep `api.yaml`, `.env` files, and provider keys private. They are intentionally ignored by Git.
- Give each client its own Model Mesh API key and restrict model access where appropriate.
- Set rate limits and timeouts appropriate to your provider quotas and application needs.
- Review logs and runtime health before relying on a new provider configuration in production.

## Project status

Model Mesh is actively being shaped around a narrow goal: dependable API routing for selected AI models. The public interface may evolve as that core workflow is refined.

