# CLI Mode

OpenHands can be run in an interactive CLI mode, which allows users to start an interactive session via the command line.

This mode is different from the [headless mode](headless-mode), which is non-interactive and better for scripting.

## With Python

To start an interactive OpenHands session via the command line:

1. Ensure you have followed the [Development setup instructions](https://github.com/All-Hands-AI/OpenHands/blob/main/Development.md).
2. Run the following command:

```bash
poetry run python -m openhands.core.cli
```

This command will start an interactive session where you can input tasks and receive responses from OpenHands.

You'll need to be sure to set your model, API key, and other settings via environment variables
[or the `config.toml` file](https://github.com/All-Hands-AI/OpenHands/blob/main/config.template.toml).

## With Docker

To run OpenHands in CLI mode with Docker:

1. Set the following environmental variables in your terminal:

- `SANDBOX_VOLUMES` to specify the directory you want OpenHands to access (Ex: `export SANDBOX_VOLUMES=$(pwd)/workspace:/workspace:rw`).
  - The agent works in `/workspace` by default, so mount your project directory there if you want the agent to modify files.
  - For read-only data, use a different mount path (Ex: `export SANDBOX_VOLUMES=$(pwd)/workspace:/workspace:rw,/path/to/large/dataset:/data:ro`).
- `LLM_MODEL` to the model to use (Ex: `export LLM_MODEL="anthropic/claude-3-5-sonnet-20241022"`).
- `LLM_API_KEY` to the API key (Ex: `export LLM_API_KEY="sk_test_12345"`).

2. Run the following Docker command:

```bash
docker run -it \
    --pull=always \
    -e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.36-nikolaik \
    -e SANDBOX_USER_ID=$(id -u) \
    -e SANDBOX_VOLUMES=$SANDBOX_VOLUMES \
    -e LLM_API_KEY=$LLM_API_KEY \
    -e LLM_MODEL=$LLM_MODEL \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v ~/.openhands-state:/.openhands-state \
    --add-host host.docker.internal:host-gateway \
    --name openhands-app-$(date +%Y%m%d%H%M%S) \
    docker.all-hands.dev/all-hands-ai/openhands:0.36 \
    python -m openhands.core.cli
```

This command will start an interactive session in Docker where you can input tasks and receive responses from OpenHands.

The `-e SANDBOX_USER_ID=$(id -u)` is passed to the Docker command to ensure the sandbox user matches the host user’s
permissions. This prevents the agent from creating root-owned files in the mounted workspace.
