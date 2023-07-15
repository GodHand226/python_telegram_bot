> **Note**
> Support for functions (aka plugins) is being added! Check out [this discussion](https://github.com/n3d1117/chatgpt-telegram-bot/discussions/345) for more info and contribute!

# ChatGPT Telegram Bot
![python-version](https://img.shields.io/badge/python-3.9-blue.svg)
[![openai-version](https://img.shields.io/badge/openai-0.27.8-orange.svg)](https://openai.com/)
[![license](https://img.shields.io/badge/License-GPL%202.0-brightgreen.svg)](LICENSE)
[![Publish Docker image](https://github.com/n3d1117/chatgpt-telegram-bot/actions/workflows/publish.yaml/badge.svg)](https://github.com/n3d1117/chatgpt-telegram-bot/actions/workflows/publish.yaml)

A [Telegram bot](https://core.telegram.org/bots/api) that integrates with OpenAI's _official_ [ChatGPT](https://openai.com/blog/chatgpt/), [DALL·E](https://openai.com/product/dall-e-2) and [Whisper](https://openai.com/research/whisper) APIs to provide answers. Ready to use with minimal configuration required.

## Screenshots
![demo](https://user-images.githubusercontent.com/11541888/225114786-0d639854-b3e1-4214-b49a-e51ce8c40387.png)

## Features
- [x] Support markdown in answers
- [x] Reset conversation with the `/reset` command
- [x] Typing indicator while generating a response
- [x] Access can be restricted by specifying a list of allowed users
- [x] Docker and Proxy support
- [x] Image generation using DALL·E via the `/image` command
- [x] Transcribe audio and video messages using Whisper (may require [ffmpeg](https://ffmpeg.org))
- [x] Automatic conversation summary to avoid excessive token usage
- [x] Track token usage per user - by [@AlexHTW](https://github.com/AlexHTW)
- [x] Get personal token usage statistics and cost per day/month via the `/stats` command - by [@AlexHTW](https://github.com/AlexHTW)
- [x] User budgets and guest budgets - by [@AlexHTW](https://github.com/AlexHTW)
- [x] Stream support
- [x] GPT-4 support
  - If you have access to the GPT-4 API, simply change the `OPENAI_MODEL` parameter to `gpt-4`
- [x] Localized bot language
  - Available languages :gb: :de: :ru: :tr: :it: :finland: :es: :indonesia: :netherlands: :cn: :taiwan: :vietnam: :iran: :brazil: :ukraine:
- [x] Improved inline queries support for group and private chats - by [@bugfloyd](https://github.com/bugfloyd)
  - To use this feature, enable inline queries for your bot in BotFather via the `/setinline` [command](https://core.telegram.org/bots/inline)
- [x] (NEW!) Support *new models* [announced on June 13, 2023](https://openai.com/blog/function-calling-and-other-api-updates)
- [x] (NEW!) Support *functions* (plugins) to extend the bot's functionality with 3rd party services
  - See [here](#available-plugins) for a list of available plugins

## Additional features - help needed!
If you'd like to help, check out the [issues](https://github.com/n3d1117/chatgpt-telegram-bot/issues) section and contribute!  
If you want to help with translations, check out the [Translations Manual](https://github.com/n3d1117/chatgpt-telegram-bot/discussions/219)

PRs are always welcome!

## Prerequisites
- Python 3.9+
- A [Telegram bot](https://core.telegram.org/bots#6-botfather) and its token (see [tutorial](https://core.telegram.org/bots/tutorial#obtain-your-bot-token))
- An [OpenAI](https://openai.com) account (see [configuration](#configuration) section)

## Getting started

### Configuration
Customize the configuration by copying `.env.example` and renaming it to `.env`, then editing the required parameters as desired:

| Parameter                   | Description                                                                                                                                                                                                                   |
|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `OPENAI_API_KEY`            | Your OpenAI API key, you can get it from [here](https://platform.openai.com/account/api-keys)                                                                                                                                 |
| `TELEGRAM_BOT_TOKEN`        | Your Telegram bot's token, obtained using [BotFather](http://t.me/botfather) (see [tutorial](https://core.telegram.org/bots/tutorial#obtain-your-bot-token))                                                                  |
| `ADMIN_USER_IDS`            | Telegram user IDs of admins. These users have access to special admin commands, information and no budget restrictions. Admin IDs don't have to be added to `ALLOWED_TELEGRAM_USER_IDS`. **Note**: by default, no admin (`-`) |
| `ALLOWED_TELEGRAM_USER_IDS` | A comma-separated list of Telegram user IDs that are allowed to interact with the bot (use [getidsbot](https://t.me/getidsbot) to find your user ID). **Note**: by default, *everyone* is allowed (`*`)                       |

### Optional configuration
The following parameters are optional and can be set in the `.env` file:

#### Budgets
| Parameter             | Description                                                                                                                                                                                                                                                                                                                                                                               | Default value      |
|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| `BUDGET_PERIOD`       | Determines the time frame all budgets are applied to. Available periods: `daily` *(resets budget every day)*, `monthly` *(resets budgets on the first of each month)*, `all-time` *(never resets budget)*. See the [Budget Manual](https://github.com/n3d1117/chatgpt-telegram-bot/discussions/184) for more information                                                                  | `monthly`          |
| `USER_BUDGETS`        | A comma-separated list of $-amounts per user from list `ALLOWED_TELEGRAM_USER_IDS` to set custom usage limit of OpenAI API costs for each. For `*`- user lists the first `USER_BUDGETS` value is given to every user. **Note**: by default, *no limits* for any user (`*`). See the [Budget Manual](https://github.com/n3d1117/chatgpt-telegram-bot/discussions/184) for more information | `*`                |
| `GUEST_BUDGET`        | $-amount as usage limit for all guest users. Guest users are users in group chats that are not in the `ALLOWED_TELEGRAM_USER_IDS` list. Value is ignored if no usage limits are set in user budgets (`USER_BUDGETS`=`*`). See the [Budget Manual](https://github.com/n3d1117/chatgpt-telegram-bot/discussions/184) for more information                                                   | `100.0`            |
| `TOKEN_PRICE`         | $-price per 1000 tokens used to compute cost information in usage statistics. Source: https://openai.com/pricing                                                                                                                                                                                                                                                                          | `0.002`            |
| `IMAGE_PRICES`        | A comma-separated list with 3 elements of prices for the different image sizes: `256x256`, `512x512` and `1024x1024`. Source: https://openai.com/pricing                                                                                                                                                                                                                                  | `0.016,0.018,0.02` |
| `TRANSCRIPTION_PRICE` | USD-price for one minute of audio transcription. Source: https://openai.com/pricing                                                                                                                                                                                                                                                                                                       | `0.006`            |

Check out the [Budget Manual](https://github.com/n3d1117/chatgpt-telegram-bot/discussions/184) for possible budget configurations.

#### Additional optional configuration options
| Parameter                          | Description                                                                                                                                                                                                                                                           | Default value                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| `ENABLE_QUOTING`                   | Whether to enable message quoting in private chats                                                                                                                                                                                                                    | `true`                              |
| `ENABLE_IMAGE_GENERATION`          | Whether to enable image generation via the `/image` command                                                                                                                                                                                                           | `true`                              |
| `ENABLE_TRANSCRIPTION`             | Whether to enable transcriptions of audio and video messages                                                                                                                                                                                                          | `true`                              |
| `PROXY`                            | Proxy to be used for OpenAI and Telegram bot (e.g. `http://localhost:8080`)                                                                                                                                                                                           | -                                   |
| `OPENAI_MODEL`                     | The OpenAI model to use for generating responses. You can find all available models [here](https://platform.openai.com/docs/models/)                                                                                                                                  | `gpt-3.5-turbo`                     |
| `ASSISTANT_PROMPT`                 | A system message that sets the tone and controls the behavior of the assistant                                                                                                                                                                                        | `You are a helpful assistant.`      |
| `SHOW_USAGE`                       | Whether to show OpenAI token usage information after each response                                                                                                                                                                                                    | `false`                             |
| `STREAM`                           | Whether to stream responses. **Note**: incompatible, if enabled, with `N_CHOICES` higher than 1                                                                                                                                                                       | `true`                              |
| `MAX_TOKENS`                       | Upper bound on how many tokens the ChatGPT API will return                                                                                                                                                                                                            | `1200` for GPT-3, `2400` for GPT-4  |
| `MAX_HISTORY_SIZE`                 | Max number of messages to keep in memory, after which the conversation will be summarised to avoid excessive token usage                                                                                                                                              | `15`                                |
| `MAX_CONVERSATION_AGE_MINUTES`     | Maximum number of minutes a conversation should live since the last message, after which the conversation will be reset                                                                                                                                               | `180`                               |
| `VOICE_REPLY_WITH_TRANSCRIPT_ONLY` | Whether to answer to voice messages with the transcript only or with a ChatGPT response of the transcript                                                                                                                                                             | `false`                             |
| `VOICE_REPLY_PROMPTS`              | A semicolon separated list of phrases (i.e. `Hi bot;Hello chat`). If the transcript starts with any of them, it will be treated as a prompt even if `VOICE_REPLY_WITH_TRANSCRIPT_ONLY` is set to `true`                                                               | -                                   |
| `N_CHOICES`                        | Number of answers to generate for each input message. **Note**: setting this to a number higher than 1 will not work properly if `STREAM` is enabled                                                                                                                  | `1`                                 |
| `TEMPERATURE`                      | Number between 0 and 2. Higher values will make the output more random                                                                                                                                                                                                | `1.0`                               |
| `PRESENCE_PENALTY`                 | Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far                                                                                                                                                      | `0.0`                               |
| `FREQUENCY_PENALTY`                | Number between -2.0 and 2.0. Positive values penalize new tokens based on their existing frequency in the text so far                                                                                                                                                 | `0.0`                               |
| `IMAGE_SIZE`                       | The DALL·E generated image size. Allowed values: `256x256`, `512x512` or `1024x1024`                                                                                                                                                                                  | `512x512`                           |
| `GROUP_TRIGGER_KEYWORD`            | If set, the bot in group chats will only respond to messages that start with this keyword                                                                                                                                                                             | -                                   |
| `IGNORE_GROUP_TRANSCRIPTIONS`      | If set to true, the bot will not process transcriptions in group chats                                                                                                                                                                                                | `true`                              |
| `BOT_LANGUAGE`                     | Language of general bot messages. Currently available: `en`, `de`, `ru`, `tr`, `it`, `fi`, `es`, `id`, `nl`, `zh-cn`, `zh-tw`, `vi`, `fa`, `pt-br`, `uk`.  [Contribute with additional translations](https://github.com/n3d1117/chatgpt-telegram-bot/discussions/219) | `en`                                |

Check out the [official API reference](https://platform.openai.com/docs/api-reference/chat) for more details.

#### Functions
| Parameter                         | Description                                                                                                                                      | Default value                       |
|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| `ENABLE_FUNCTIONS`                | Whether to use functions (aka plugins). You can read more about functions [here](https://openai.com/blog/function-calling-and-other-api-updates) | `true` (if available for the model) |
| `FUNCTIONS_MAX_CONSECUTIVE_CALLS` | Maximum number of back-to-back function calls to be made by the model in a single response, before displaying a user-facing message              | `10`                                |
| `PLUGINS`                         | List of plugins to enable (see below for a full list), e.g: `PLUGINS=wolfram,weather`                                                            | -                                   |
| `SHOW_PLUGINS_USED`               | Whether to show which plugins were used for a response                                                                                           | `false`                             |

#### Available plugins
| Name                      | Description                                                                                                                                         | Required environment variable(s)                                     | Dependency                             |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|----------------------------------------|
| `weather`                 | Daily weather and 7-day forecast for any location (powered by [Open-Meteo](https://open-meteo.com))                                                 | -                                                                    |                                        |
| `wolfram`                 | WolframAlpha queries (powered by [WolframAlpha](https://www.wolframalpha.com))                                                                      | `WOLFRAM_APP_ID`                                                     | `pip install wolframalpha~=5.0.0`      |
| `ddg_web_search`          | Web search (powered by [DuckDuckGo](https://duckduckgo.com))                                                                                        | -                                                                    | `pip install duckduckgo_search~=3.8.3` |
| `ddg_translate`           | Translate text to any language (powered by [DuckDuckGo](https://duckduckgo.com))                                                                    | -                                                                    | `pip install duckduckgo_search~=3.8.3` |       
| `ddg_image_search`        | Search image or GIF (powered by [DuckDuckGo](https://duckduckgo.com))                                                                               | -                                                                    | `pip install duckduckgo_search~=3.8.3` |
| `crypto`                  | Live cryptocurrencies rate (powered by [CoinCap](https://coincap.io)) - by [@stumpyfr](https://github.com/stumpyfr)                                 | -                                                                    |                                        |
| `spotify`                 | Spotify top tracks/artists, currently playing song and content search (powered by [Spotify](https://spotify.com)). Requires one-time authorization. | `SPOTIFY_CLIENT_ID`, `SPOTIFY_CLIENT_SECRET`, `SPOTIFY_REDIRECT_URI` | `pip install spotipy~=2.23.0`          |
| `worldtimeapi`            | Get latest world time (powered by [WorldTimeAPI](https://worldtimeapi.org/))                                                                        | `WORLDTIME_DEFAULT_TIMEZONE`                                         |                                        |
| `dice`                    | Send a dice in the chat!                                                                                                                            | -                                                                    |                                        |
| `youtube_audio_extractor` | Extract audio from YouTube videos                                                                                                                   | -                                                                    | `pip install pytube~=15.0.0`           |
| `deepl_translate`         | Translate text to any language (powered by [DeepL](https://deepl.com)) - by [@LedyBacer](https://github.com/LedyBacer)                              | `DEEPL_API_KEY`                                                      |                                        |
| `gtts_text_to_speech`     | Text to speech (powered by Google Translate APIs)                                                                                                   | -                                                                    | `pip install gtts~=2.3.2`              |
| `whois`                   | Query the whois domain database                                                                                                                     | -                                                                    | `pip install whois~=0.9.27`            |

**Note**: some plugins have additional dependencies that are not listed in the `requirements.txt` file. If you plan on using these plugins, you can install them manually using the command above (see the `Dependency` column).

#### Environment variables
| Variable                          | Description                                                                                                                                                                                     | Default value                       |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| `WOLFRAM_APP_ID`                  | Wolfram Alpha APP ID (required only for the `wolfram` plugin, you can get one [here](https://products.wolframalpha.com/simple-api/documentation))                                               | -                                   |
| `SPOTIFY_CLIENT_ID`               | Spotify app Client ID (required only for the `spotify` plugin, you can find it on the [dashboard](https://developer.spotify.com/dashboard/))                                                    | -                                   |
| `SPOTIFY_CLIENT_SECRET`           | Spotify app Client Secret (required only for the `spotify` plugin, you can find it on the [dashboard](https://developer.spotify.com/dashboard/))                                                | -                                   |
| `SPOTIFY_REDIRECT_URI`            | Spotify app Redirect URI (required only for the `spotify` plugin, you can find it on the [dashboard](https://developer.spotify.com/dashboard/))                                                 | -                                   |
| `WORLDTIME_DEFAULT_TIMEZONE`      | Default timezone to use, i.e. `Europe/Rome` (required only for the `worldtimeapi` plugin, you can get TZ Identifiers from [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)) | -                                   |
| `DUCKDUCKGO_SAFESEARCH`           | DuckDuckGo safe search (`on`, `off` or `moderate`) (optional, applies to `ddg_web_search` and `ddg_image_search`)                                                                               | `moderate`                          |
| `DEEPL_API_KEY`                   | DeepL API key (required for the `deepl` plugin, you can get one [here](https://www.deepl.com/pro-api?cta=header-pro-api))                                                                       | -                                   |

### Installing
Clone the repository and navigate to the project directory:

```shell
git clone https://github.com/n3d1117/chatgpt-telegram-bot.git
cd chatgpt-telegram-bot
```

#### From Source
1. Create a virtual environment:
```shell
python -m venv venv
```

2. Activate the virtual environment:
```shell
# For Linux or macOS:
source venv/bin/activate

# For Windows:
venv\Scripts\activate
```

3. Install the dependencies using `requirements.txt` file:
```shell
pip install -r requirements.txt
```

4. Use the following command to start the bot:
```
python bot/main.py
```

#### Using Docker Compose

Run the following command to build and run the Docker image:
```shell
docker compose up
```

#### Ready-to-use Docker images
You can also use the Docker image from [Docker Hub](https://hub.docker.com/r/n3d1117/chatgpt-telegram-bot):
```shell
docker pull n3d1117/chatgpt-telegram-bot:latest
docker run -it --env-file .env n3d1117/chatgpt-telegram-bot
```

or using the [GitHub Container Registry](https://github.com/n3d1117/chatgpt-telegram-bot/pkgs/container/chatgpt-telegram-bot/):

```shell
docker pull ghcr.io/n3d1117/chatgpt-telegram-bot:latest
docker run -it --env-file .env ghcr.io/n3d1117/chatgpt-telegram-bot
```

#### Docker manual build
```shell
docker build -t chatgpt-telegram-bot .
docker run -it --env-file .env chatgpt-telegram-bot
```

## Credits
- [ChatGPT](https://chat.openai.com/chat) from [OpenAI](https://openai.com)
- [python-telegram-bot](https://python-telegram-bot.org)
- [jiaaro/pydub](https://github.com/jiaaro/pydub)

## Disclaimer
This is a personal project and is not affiliated with OpenAI in any way.

## License
This project is released under the terms of the GPL 2.0 license. For more information, see the [LICENSE](LICENSE) file included in the repository.
