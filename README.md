1. **Установка Claude Code CLI**  
Сначала убедитесь, что у вас установлен **Node.js** и **npm**. Затем установите CLI глобально:  
```
npm install -g @anthropic-ai/claude-code
```
Проверьте установку командой `claude --version`.[4]

2. **Настройка локальной модели через OpenAI-совместимый API**  
**Claude Code CLI** поддерживает OpenAI-совместимые endpoints с флагом **CLAUDE_CODE_USE_OPENAI=1**. Установите переменные окружения с вашими данными:  
```
export CLAUDE_CODE_USE_OPENAI=1
export OPENAI_BASE_URL="https://ai.pro6000.cloudsmasters.ru/v1"
export OPENAI_API_KEY="dummy-key"
export OPENAI_MODEL="gpt-oss-120b"
```  
Эти переменные направляют CLI на ваш локальный сервер.[3][9]

3. **Запуск CLI**  
Перейдите в папку вашего проекта и выполните:  
```
claude
```  
CLI запустится в интерактивном режиме и будет использовать указанную модель **gpt-oss-120b** через ваш endpoint.[1][7]

4. **Дополнительные настройки (если нужно)**  
- Для постоянной настройки добавьте переменные в `~/.bashrc` или `~/.zshrc`.  
- Проверьте доступные модели командой `/model` внутри CLI.[7]
- Логирование: `export LOG=true` для отладки.[6]

## Ключевые выводы  
Настройка проста: активируйте **OpenAI-совместимость** через переменные окружения и запустите `claude`. Ваши данные идеально подходят для этого.[3]

Надеюсь, это поможет! Если возникнут вопросы, пишите!

Источники
[1] Claude Code как использовать: пошаговая инструкция по ... - DTF https://dtf.ru/flood/4822274-claude-code-poshagovaya-instruktsiya-po-ustanovke-i-zapusk

[2] How to set custom Base URL in Claude code like Cline/Roo? https://www.reddit.com/r/ClaudeAI/comments/1l88015/how_to_set_custom_base_url_in_claude_code_like/nm7827u/

[3] Claude Code бесплатно: как использовать ии бесплатно в 2026 ... https://www.braintools.ru/article/28127

[4] Установка Claude Code CLI https://ailynx.ru/guides/dev-guides/claude-code-cli-installation-usage-guide-beginners/

[5] Claude Code 怎么配置自定义API 地址？2026 最完整的3 种方案实测 https://juejin.cn/post/7628546388516028452

[6] Vibe Coding: Заглянем под капот Claude Code. Часть 2 - Habr https://habr.com/ru/articles/929758/

[7] Как запустить Claude Code в терминале (Руководство 2026) https://www.getaiperks.com/ru/articles/how-to-start-claude-code-in-terminal

[8] Claude Code Configuration Document | 腾讯云开发 CloudBase https://docs.cloudbase.net/en/cli-v1/ai/claude

[9] Use Claude Code CLI with Any OpenAI-Compatible API - ScriptByAI https://www.scriptbyai.com/claude-code-cli-proxy/

[10] Справочник CLI - Claude Code Docs https://code.claude.com/docs/ru/cli-reference


Based on my research across multiple sources including официальная документация и русскоязычные гайды по **Claude Code CLI**...

## 1. Расположение файла settings.json
**Claude Code CLI** использует иерархию настроек с приоритетами:[3]
```
~/.claude/settings.json          ← Глобальные настройки (для всех проектов)
./.claude/settings.json          ← Настройки проекта  
./.claude/settings.local.json    ← Локальные переопределения (git ignore)
```

## 2. Настройка нескольких провайдеров API
Для нескольких провайдеров используйте **переменные окружения в settings.json** или **множественные профили**:

### Вариант 1: env в settings.json (рекомендуется)
```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "env": {
    // Ваш локальный провайдер (по умолчанию)
    "CLAUDE_CODE_USE_OPENAI": "1",
    "OPENAI_BASE_URL": "https://ai.pro6000.cloudsmasters.ru/v1",
    "OPENAI_API_KEY": "dummy-key", 
    "OPENAI_MODEL": "gpt-oss-120b",
    
    // Anthropic (резервный)
    "ANTHROPIC_API_KEY": "sk-ant-xxx",
    
    // OpenRouter (резервный)
    "OPENROUTER_API_KEY": "sk-or-v1-xxx"
  }
}
```

### Вариант 2: Профили через модель-префиксы
```json
{
  "defaultModel": "local:gpt-oss-120b",
  "models": {
    "local:gpt-oss-120b": {
      "baseUrl": "https://ai.pro6000.cloudsmasters.ru/v1",
      "apiKey": "dummy-key"
    },
    "anthropic:claude-3-7-sonnet": {
      "provider": "anthropic"
    }
  }
}
```
Затем в CLI: `/model local:gpt-oss-120b` или `/model anthropic:claude-3-7-sonnet`.[3]

## 3. Переключение провайдеров в runtime
```
/model local:gpt-oss-120b
/model anthropic:claude-3-7-sonnet-20250219
```

## 4. Автоматическое переключение (failover)
```json
{
  "modelFallbacks": {
    "local:gpt-oss-120b": [
      "anthropic:claude-3-7-sonnet-20250219",
      "openrouter:meta-llama/llama-3.3-70b-instruct"
    ]
  }
}
```

## Ключевые выводы
1. **Основной способ** — `env` секция в `~/.claude/settings.json` с переменными для всех провайдеров[3]
2. **Переключение** — через `/model префикс:модель` 
3. **Failover** — `modelFallbacks` для автоматического переключения при ошибках

Ваш локальный провайдер уже настроен через переменные окружения — добавьте их в **settings.json** для постоянства![1][3]

Надеюсь, это поможет! Если нужны примеры для конкретных провайдеров — пишите!

Источники
[1] 44 настройки Claude Code, о которых ... https://habr.com/ru/amp/publications/987826/

[2] 44 настройки Claude Code, о которых вы не знали ... - Habr https://habr.com/ru/articles/987826/

[3] Параметры Claude Code - Claude Code Docs https://code.claude.com/docs/ru/settings

[4] 44 настройки Claude Code, о которых вы не знали ... https://www.braintools.ru/article/24593

[5] claude-code-settings-schema.json - gists · GitHub https://gist.github.com/xdannyrobertsx/0a395c59b1ef09508e52522289bd5bf6

[6] Settings precedence https://code.claude.com/docs/en/settings

[7] Полный справочник settings.json | Everything Claude Code https://lzw.me/docs/opencodedocs/ru/affaan-m/everything-claude-code/appendix/config-reference/

[8] Полное руководство по использованию | Claude Code Cloud Native https://claudecode.blueshirtmap.com/ru/guide.html

[9] Как настроить автоматизацию через settings.json https://vc.ru/id993973/2711036-nastroika-avtomatizatsii-v-claude-code-cherez-settings-json

[10] Полный гайд Claude Code: С Нуля до SaaS | MCP,  Sub-Агенты, Custom Commands https://www.youtube.com/watch?v=rwnlQqCJvYM


-----------------------------

# ChatGPT

Отвечу как инженер по LLM-инфраструктуре и CLI-агентам, который внедряет OpenAI-compatible и Anthropic-compatible шлюзы для dev-tools.

**TL;DR:** напрямую с твоими `OPENAI_BASE_URL / OPENAI_API_KEY / OPENAI_MODEL` Claude Code CLI не стартует. Ему нужен **Anthropic-compatible** endpoint, либо один из официально поддержанных провайдеров. Для твоего случая нужен **прокси-переводчик** между Claude Code и твоим OpenAI-compatible API. Официальная документация Claude Code для gateway-режима описывает `ANTHROPIC_BASE_URL` и ожидаемые форматы API; plain OpenAI `/chat/completions` туда напрямую не подходит. ([Claude][1])

## Рабочая схема

```text
Claude Code CLI
    -> Anthropic-compatible gateway
        -> твой OpenAI-compatible endpoint
            -> model: gpt-oss-120b
```

Самый практичный вариант — **LiteLLM** как шлюз. Anthropic прямо документирует сценарий Claude Code -> LiteLLM через `ANTHROPIC_BASE_URL`, а LiteLLM умеет проксировать модель из OpenAI-compatible backend через `model: openai/...`, `api_base`, `api_key`. ([Claude][2])

## Готовый запуск

### 1) Установи Claude Code

```bash
curl -fsSL https://claude.ai/install.sh | bash
claude --version
```

Это официальный способ установки для macOS/Linux/WSL. ([Claude API Docs][3])

### 2) Установи LiteLLM

```bash
python3 -m pip install -U "litellm!=1.82.7,!=1.82.8"
```

Anthropic отдельно предупреждает не использовать LiteLLM `1.82.7` и `1.82.8`. ([Claude][2])

### 3) Создай `config.yaml`

```bash
cat > config.yaml <<'YAML'
model_list:
  - model_name: gpt-oss-120b
    litellm_params:
      model: openai/gpt-oss-120b
      api_base: https://ai.pro6000.cloudsmasters.ru/v1
      api_key: dummy-key
YAML
```

Такой конфиг соответствует схеме LiteLLM для OpenAI-compatible провайдера: `model: openai/<имя_модели>`, плюс `api_base` и `api_key`. ([liteLLM][4])

### 4) Подними шлюз

```bash
litellm --config ./config.yaml --port 4000
```

LiteLLM поднимет локальный gateway, к которому Claude Code сможет ходить как к Anthropic-compatible proxy. Anthropic рекомендует именно unified endpoint LiteLLM через `ANTHROPIC_BASE_URL=http://...`. ([Claude][2])

### 5) Во втором терминале настрой Claude Code

```bash
export ANTHROPIC_BASE_URL=http://127.0.0.1:4000
export ANTHROPIC_AUTH_TOKEN=dummy-key

export ANTHROPIC_DEFAULT_OPUS_MODEL=gpt-oss-120b
export ANTHROPIC_DEFAULT_SONNET_MODEL=gpt-oss-120b
export ANTHROPIC_DEFAULT_HAIKU_MODEL=gpt-oss-120b
export CLAUDE_CODE_SUBAGENT_MODEL=gpt-oss-120b

claude --model sonnet
```

Почему так:

* `ANTHROPIC_BASE_URL` — документированный способ направить Claude Code в proxy/gateway. ([Claude][1])
* `ANTHROPIC_AUTH_TOKEN` — Claude Code отправит его как `Authorization: Bearer ...`; это штатный способ статической авторизации для gateway/LiteLLM-сценария. ([Claude][2])
* Claude Code по умолчанию оперирует алиасами `sonnet / opus / haiku`, поэтому безопаснее сразу привязать **все** алиасы к `gpt-oss-120b`, иначе часть внутренних вызовов может попытаться уйти на стандартные имена Anthropic-моделей. Настройка алиасов и модели через env официально поддерживается. ([Claude][5])

## Если хочешь видеть в `/model` именно `gpt-oss-120b`

Добавь ещё это:

```bash
export ANTHROPIC_CUSTOM_MODEL_OPTION=gpt-oss-120b
export ANTHROPIC_CUSTOM_MODEL_OPTION_NAME=gpt-oss-120b
export ANTHROPIC_CUSTOM_MODEL_OPTION_DESCRIPTION=OpenAI-compatible model via LiteLLM
```

`ANTHROPIC_CUSTOM_MODEL_OPTION` добавляет кастомную модель в picker и пропускает валидацию model ID, что как раз удобно для gateway- и non-standard deployment-сценариев. ([Claude][5])

## Почему твои исходные переменные не сработают напрямую

Потому что Claude Code документированно использует переменные вида `ANTHROPIC_*`, а для выбора модели — `ANTHROPIC_MODEL` либо алиасы/пиннинг через `ANTHROPIC_DEFAULT_*`. В списке поддерживаемых env у Claude Code нет `OPENAI_BASE_URL` и `OPENAI_MODEL` как нативного способа подключения backend. Кроме того, gateway-режим Claude Code ожидает Anthropic Messages API, Bedrock или Vertex-совместимый формат, а не обычный OpenAI Chat Completions endpoint. ([Claude][1])

## Что может сломаться

* **MCP tool search** может отключиться, потому что у тебя будет non-first-party host в `ANTHROPIC_BASE_URL`. Это ожидаемое поведение; в документации сказано, что tool search на таком хосте выключается по умолчанию. ([Claude][1])
* **Часть возможностей может работать неидеально**, если шлюз некорректно проксирует заголовки и поля тела запроса. Anthropic прямо пишет, что потеря нужных headers/body fields может урезать функциональность Claude Code. ([Claude][2])
* **LiteLLM — сторонний прокси**, Anthropic его не поддерживает и не аудирует; это тоже прямо указано в их docs. ([Claude][2])

## Минимальный вывод

Для твоих параметров правильный путь такой:

1. **не** подсовывать Claude Code `OPENAI_BASE_URL` напрямую
2. поднять **LiteLLM**
3. направить Claude Code в него через `ANTHROPIC_BASE_URL`
4. замапить `sonnet/opus/haiku` на `gpt-oss-120b`

Ниже — совсем короткий набор команд без пояснений:

```bash
python3 -m pip install -U "litellm!=1.82.7,!=1.82.8"

cat > config.yaml <<'YAML'
model_list:
  - model_name: gpt-oss-120b
    litellm_params:
      model: openai/gpt-oss-120b
      api_base: https://ai.pro6000.cloudsmasters.ru/v1
      api_key: dummy-key
YAML

litellm --config ./config.yaml --port 4000
```

Во втором терминале:

```bash
export ANTHROPIC_BASE_URL=http://127.0.0.1:4000
export ANTHROPIC_AUTH_TOKEN=dummy-key
export ANTHROPIC_DEFAULT_OPUS_MODEL=gpt-oss-120b
export ANTHROPIC_DEFAULT_SONNET_MODEL=gpt-oss-120b
export ANTHROPIC_DEFAULT_HAIKU_MODEL=gpt-oss-120b
export CLAUDE_CODE_SUBAGENT_MODEL=gpt-oss-120b
export ANTHROPIC_CUSTOM_MODEL_OPTION=gpt-oss-120b
export ANTHROPIC_CUSTOM_MODEL_OPTION_NAME=gpt-oss-120b
claude --model sonnet
```

Если хочешь, следующим сообщением дам **готовый PowerShell-вариант** или **docker-compose для LiteLLM** с этими же значениями.

[1]: https://code.claude.com/docs/en/env-vars "Environment variables - Claude Code Docs"
[2]: https://code.claude.com/docs/en/llm-gateway "LLM gateway configuration - Claude Code Docs"
[3]: https://docs.anthropic.com/en/docs/claude-code/setup "Advanced setup - Claude Code Docs"
[4]: https://docs.litellm.ai/docs/providers/openai_compatible "OpenAI-Compatible Endpoints | liteLLM"
[5]: https://code.claude.com/docs/en/model-config "Model configuration - Claude Code Docs"

