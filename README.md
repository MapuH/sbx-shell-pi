# sbx-shell-pi

Run the minimal [terminal coding harness Pi](https://pi.dev) inside a [Docker sandbox](https://docs.docker.com/ai/sandboxes/).

## Description
Docker Sandboxes (sbx) does not offer a ready-to-use template for the Pi agent. This repo provides a Pi [kit](https://docs.docker.com/ai/sandboxes/customize/kits/) which extends the [shell sandbox template](https://docs.docker.com/ai/sandboxes/agents/shell/). The kit spec can be modified as per your individual preferences.

>Kits are experimental. The kit file format, CLI commands, and experience for creating, loading, and managing kits are subject to change as the feature evolves.

## Usage

1. [Install Docker Sandboxes](https://docs.docker.com/ai/sandboxes/#get-started)

2. Set credentials
	- For [built-in](https://docs.docker.com/ai/sandboxes/security/credentials/#built-in-services) services:
	
		```console
		sbx secret set -g <service>
		```
	- For [custom](https://docs.docker.com/ai/sandboxes/security/credentials/#custom-secrets) services (e.g. the kit uses opencode go/zen provider):
	
		```console
		sbx secret set-custom -g --host opencode.ai --env OPENCODE_API_KEY --placeholder sk-opencode
		```

3. Build and run the sandbox

	```console
	sbx run --name my-sandbox-name --kit ./sbx-shell-pi pi path/to/my/project
	```
	or
	
	```console
	sbx run --name my-sandbox-name --kit "git+https://github.com/MapuH/sbx-shell-pi" pi path/to/my/project
	```

## Configuration

```console
sbx-shell-pi/
├── LICENSE
├── README.md
├── spec.yaml
└── files/home/.pi/agent/
    ├── APPEND_SYSTEM.md
    ├── settings.json
	└── agents/
		├── scout.md
    	└── worker.md
```

### spec.yaml

- **agent** - set an `entrypoint` if you'd like the sandbox to start directly in pi. I prefer to have access to the shell and start pi manually.

- **network** - the kit is configured with [network](https://docs.docker.com/ai/sandboxes/customize/kits/#network) connection to the [opencode](https://opencode.ai/) provider. The commented connection to openai is provided as an example.

- **credentials** - example [credentials](https://docs.docker.com/ai/sandboxes/customize/kits/#credentials) configuration for openai.

- **environment** - configuration for the [environment](https://docs.docker.com/ai/sandboxes/customize/kits/#environment) variables in the container. Built-in services (e.g. openai) variables are set as `proxyManaged`. Custom services (e.g. opencode) variables are set directly as `NAME: <value>` pairs using the secret placeholder as value. In both cases the agent does not have access to the real secrets as those are injected by the proxy at request time.

- **commands** - [install](https://docs.docker.com/ai/sandboxes/customize/kits/#run-commands) whatever packages you deem necessary as the root user. Install Pi as the agent user. Run additional commands as root/agent if needed.

### files

- **APPEND_SYSTEM.md** - the [sbx docs](https://docs.docker.com/ai/sandboxes/customize/kits/#memory) recommend to use a `memory` block to append to the agent's memory file at sandbox creation. However, I prefer to inject a file and use Pi's native functionality. This way instructions will be appended directly to the system prompt instead of `AGENTS.md`.

- **settings.json** - you can save your global Pi settings here, so you don't have to configure the agent every time the sandbox is re-created.

- **agents** - the kit includes two example agents: `scout` for codebase reconnaissance and `worker` for implementation. You can modify these or add your own.

## License
[MIT License](LICENSE)