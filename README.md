# ChatGPT 

## Table of Contents

- [Disclaimer](#disclaimer)
- [Introduction](#introduction)
- [ToDo](#todo)
- [Prerequisites](#prerequisites)
  - [Node](#node)
  - [PNPM](#pnpm)
  - [Openai Api Key](#openai-api-key)
- [Install dependencies](#install-dependencies)
  - [Backend/Service](#backendservice)
  - [Frontend/Web](#frontendweb)
- [Development environment setup](#development-environment-setup)
  - [Backend/Service](#backendservice-1)
  - [Frontend/Web](#frontendweb-1)
- [Environment Variables](#environment-variables)
- [Packaging](#packaging)
  - [Docker](#docker)
    - [Docker parameter example](#docker-parameter-example)
    - [Docker build \& Run](#docker-build--run)
    - [Docker compose](#docker-compose)
  - [Railway Deployment](#railway-deployment)
    - [Railway Environment Variables](#railway-environment-variables)
  - [Manual/CI](#manualci)
    - [Backend/Service](#backendservice-2)
    - [Frontend/Web](#frontendweb-2)
- [FAQ](#faq)

## Disclaimer

> Disclaimer: This project is currently published on GitHub, based on the MIT license, free and used as open source learning. And there will be no sales of accounts, paid services, discussion groups, discussion groups, etc. in any form. Beware of being deceived.

## Introduction

Supports dual models, providing two unofficial `ChatGPT API` methods

| How | Free? | Reliability | Quality |
| --------------------------------------------------- | ------ | ---------- | ---- |
| `ChatGPTAPI(gpt-3.5-turbo-0301)` | No | Reliable | Relatively stupid |
| `ChatGPTUnofficialProxyAPI(webpage accessToken)` | yes | relatively unreliable | smart |

Compared:
1. `ChatGPTAPI` uses `gpt-3.5-turbo` to call `ChatGPT` through `OpenAI` official `API`
2. `ChatGPTUnofficialProxyAPI` uses an unofficial proxy server to access `ChatGPT`’s backend `API`, bypassing `Cloudflare` (depends on third-party servers, and has a rate limit)

warn:
1. You should use the `API` method first
2. When using `API`, if the network is not available, it means that the country is blocked, and you need to build your own proxy. Never use other people's public proxy, it is dangerous.
3. When using the `accessToken` method, the reverse proxy will expose your access token to third parties, which should not have any adverse effects, but please consider the risks before using this method.
4. When using `accessToken`, no matter whether you are a domestic or foreign machine, you will use a proxy. The default proxy is [acheong08](https://github.com/acheong08) `https://bypass.churchless.tech/api/conversation`, which is not a backdoor or monitoring, unless you have the ability to turn over by yourself `CF` verification, please know before using. [Community Proxy](https://github.com/transitive-bullshit/chatgpt-api#reverse-proxy) (Note: only these two are recommended, other third-party sources, please identify by yourself)
5. When publishing the project to the public network, you should set the `AUTH_SECRET_KEY` variable to add your password access rights, and you should also modify the `title` in `index.html` to prevent it from being searched by keywords.

Switching method:
1. Enter the `service/.env.example` file and copy the content to the `service/.env` file
2. To use `OpenAI API Key`, please fill in `OPENAI_API_KEY` field [(get apiKey)](https://platform.openai.com/overview)
3. To use `Web API`, please fill in `OPENAI_ACCESS_TOKEN` field [(get accessToken)](https://chat.openai.com/api/auth/session)
4. When both exist, `OpenAI API Key` takes precedence

Environment variables:

Please check all parameter variables or [here](#environment variable)

```
/service/.env.example
```

## ToDo
[✓] Dual Model

[✓] Multi-session storage and context logic

[✓] Formatting and beautifying processing of message types such as codes

[✓] Access Control

[✓] Data import, export

[✓] Save message to local picture

[✓] Multilingual interface

[✓] Interface themes

[✗] More...

## Prerequisites

### Node

`node` requires `^16 || ^18 || ^19` version (`node >= 14` needs to install [fetch polyfill](https://github.com/developit/unfetch#usage-as-a-polyfill )), use [nvm](https://github.com/nvm-sh/nvm) to manage multiple local `node` versions

```shell
node -v
```

### PNPM
If you don't have `pnpm` installed
```shell
npm install pnpm -g
```

### Openai Api Key
Get `Openai Api Key` or `accessToken` and fill in local environment variables [Jump](#Introduction)

```
# service/.env file

# OpenAI API Key - https://platform.openai.com/overview
OPENAI_API_KEY=

# change this to an `accessToken` extracted from the ChatGPT site's `https://chat.openai.com/api/auth/session` response
OPENAI_ACCESS_TOKEN=
```

## Install dependencies

> In order to simplify the understanding burden of `back-end developers`, the front-end `workspace` mode is not adopted, but stored in folders. If you only need to do secondary development on the front-end page, just delete the `service` folder.

### Backend/Service

Go to the folder `/service` and run the following command

```shell
pnpm install
```

### Frontend/Web
Run the following command in the root directory
```shell
pnpm bootstrap
```

## Development environment setup
### Backend/Service

Go to the folder `/service` and run the following command

```shell
pnpm start
```

### Frontend/Web
Run the following command in the root directory
```shell
pnpm dev
```

## Environment variables

The `API` is available:

- Choose one of `OPENAI_API_KEY` and `OPENAI_ACCESS_TOKEN`
- `OPENAI_API_MODEL` set model, optional, default: `gpt-3.5-turbo`
- `OPENAI_API_BASE_URL` set interface address, optional, default: `https://api.openai.com`
- `OPENAI_API_DISABLE_DEBUG` set the interface to close the debug log, optional, default: empty is not closed

`ACCESS_TOKEN` is available:

- You can choose between `OPENAI_ACCESS_TOKEN` and `OPENAI_API_KEY`. When they exist at the same time, `OPENAI_API_KEY` takes precedence
- `API_REVERSE_PROXY` set reverse proxy, optional, default: `https://bypass.churchless.tech/api/conversation`, [Community](https://github.com/transitive-bullshit/chatgpt-api# reverse-proxy) (Note: only these two are recommended, other third-party sources, please identify by yourself)

General:

- `AUTH_SECRET_KEY` access authorization key, optional
- `MAX_REQUEST_PER_HOUR` maximum number of requests per hour, optional, default unlimited
- `TIMEOUT_MS` timeout in milliseconds, optional
- `SOCKS_PROXY_HOST` works with `SOCKS_PROXY_PORT`, optional
- `SOCKS_PROXY_PORT` works with `SOCKS_PROXY_HOST`, optional
- `HTTPS_PROXY` supports `http`, `https`, `socks5`, optional
- `ALL_PROXY` supports `http`, `https`, `socks5`, optional

## Packaging

### Docker

#### Docker parameter example

![docker](./docs/docker.png)

#### Docker build & run

```bash
docker build -t chatgpt-web .

# run in the foreground
docker run --name chatgpt-web --rm -it -p 127.0.0.1:3002:3002 --env OPENAI_API_KEY=your_api_key chatgpt-web

# Background process
docker run --name chatgpt-web -d -p 127.0.0.1:3002:3002 --env OPENAI_API_KEY=your_api_key chatgpt-web

# run address
http://localhost:3002/
```

#### Docker compose

[Hub address](https://hub.docker.com/repository/docker/chenzhaoyu94/chatgpt-web/general)

```yml
version: '3'

services:
  app:
    image: chenzhaoyu94/chatgpt-web # Always use latest, and re-pull the tag image when updating
    ports:
      - 127.0.0.1:3002:3002
    environment:
      # pick one of two
      OPENAI_API_KEY: sk-xxx
      # pick one of two
      OPENAI_ACCESS_TOKEN: xxx
      # API interface address, optional, available when OPENAI_API_KEY is set
      OPENAI_API_BASE_URL: xxx
      # API model, optional, available when OPENAI_API_KEY is set, https://platform.openai.com/docs/models
      # gpt-4, gpt-4-0314, gpt-4-32k, gpt-4-32k-0314, gpt-3.5-turbo, gpt-3.5-turbo-0301, text-davinci-003, text-davinci-002, code-davinci-002
      OPENAI_API_MODEL: xxx
      # reverse proxy, optional
      API_REVERSE_PROXY: xxx
      # access key, optional
      AUTH_SECRET_KEY: xxx
      # The maximum number of requests per hour, optional, default unlimited
      MAX_REQUEST_PER_HOUR: 0
      # Timeout in milliseconds, optional
      TIMEOUT_MS: 60000
      # Socks proxy, optional, works with SOCKS_PROXY_PORT
      SOCKS_PROXY_HOST: xxx
      # Socks proxy port, optional, effective when combined with SOCKS_PROXY_HOST
      SOCKS_PROXY_PORT: xxx
      # HTTPS proxy, optional, supports http, https, socks5
      HTTPS_PROXY: http://xxx:7890
```
- `OPENAI_API_BASE_URL` optional, available when `OPENAI_API_KEY` is set
- `OPENAI_API_MODEL` optional, available when `OPENAI_API_KEY` is set     

### Railway Deployment

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template/yytmgc)

#### Railway Environment Variables

| Environment variable name | Mandatory | Remarks |
| --------------------- | ---------------------- | ----------------------------------------------------------------------------------------------------- |
| `PORT` | Required | Default `3002`
| `AUTH_SECRET_KEY` | optional | access authorization key |
| `MAX_REQUEST_PER_HOUR` | optional | maximum number of requests per hour, optional, default unlimited |
| `TIMEOUT_MS` | optional | timeout in milliseconds |
| `OPENAI_API_KEY` | `OpenAI API` alternative | `apiKey` required to use `OpenAI API` [(get apiKey)](https://platform.openai.com/overview) |
| `OPENAI_ACCESS_TOKEN` | `Web API` choose one | `accessToken` required to use `Web API` [(get accessToken)](https://chat.openai.com/api/auth/session) |
| `OPENAI_API_BASE_URL` | optional, available when using `OpenAI API` | `API` interface address |
| `OPENAI_API_MODEL` | Optional, available when `OpenAI API` | `API` model |
| `API_REVERSE_PROXY` | optional, available for `Web API` | `Web API` reverse proxy address [details](https://github.com/transitive-bullshit/chatgpt-api#reverse-proxy) |
| `SOCKS_PROXY_HOST` | Optional, it will take effect with `SOCKS_PROXY_PORT` | Socks proxy |
| `SOCKS_PROXY_PORT` | Optional, it will take effect with `SOCKS_PROXY_HOST` | Socks proxy port |
| `SOCKS_PROXY_USERNAME` | Optional, effective when it is used together with `SOCKS_PROXY_HOST` | Socks proxy username |
| `SOCKS_PROXY_PASSWORD` | Optional, effective when it is used together with `SOCKS_PROXY_HOST` | Socks proxy password |
| `HTTPS_PROXY` | optional | HTTPS proxy, support http, https, socks5 |
| `ALL_PROXY` | optional | all proxies proxy, support http, https, socks5 |

> Note: `Railway` modifying environment variables will restart `Deploy`

### Manual/CI

#### Backend/Service
> If you do not need the `node` interface of this project, you can omit the following operations

Copy the `service` folder to the server where you have the `node` service environment.

```shell
# Install
pnpm install

# Pack
pnpm build

# run
pnpm prod
```

PS: You can run `pnpm start` directly on the server without packaging

#### Frontend/Web

1. Modify `VITE_GLOB_API_URL` in the `.env` file in the root directory to your actual backend interface address

2. Run the following command in the root directory, and then copy the files in the `dist` folder to the root directory of your website service

[Reference Information](https://cn.vitejs.dev/guide/static-deploy.html#building-the-app)

```shell
pnpm build
```

## FAQ
Q: Why do `Git` commits always report errors?

A: Because there is commit information verification, please follow the [Commit Guide](./CONTRIBUTING.md)

Q: If only the front-end page is used, where should I change the request interface?

A: The `VITE_GLOB_API_URL` field in the `.env` file in the root directory.

Q: When the file is saved, it all explodes?

A: `vscode` Please install the project recommended plugin, or manually install the `Eslint` plugin.

Q: Is there no typewriter effect on the front end?

A: One possible reason is that after the Nginx reverse proxy is enabled and the buffer is enabled, Nginx will try to buffer a certain size of data from the backend and then send it to the browser. Please try to add `proxy_buffering off;` after the reverse parameter, and then reload Nginx. The same goes for other web server configurations.