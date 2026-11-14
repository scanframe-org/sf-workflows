# GitLab CI Runner Configuration

This directory contains configuration files for a self-hosted GitLab CI runner that executes jobs inside Docker containers.

## Files

| File | Description |
|------|-------------|
| `gitlab-runner.sh` | Helper script for managing the GitLab runner Docker container (register, run, start, stop, etc.). |
| `config/tpl.config.toml` | Template — copy and fill in your values to create `config.toml`. |
| `config/config.toml` | Example configuration with sample values (not for production use as-is). |
| `config/tpl.runner_system_id` | Instructions for the `.runner_system_id` file. |
| `config/.runner_system_id` | Placeholder — copy your existing System-ID here to keep a single runner entry across restarts. |

## Setup

### 1. Register the runner on GitLab

On your GitLab instance, go to **Settings > CI/CD > Runners** (project or group level) and note the **registration token**.

### 2. Configure the runner

Copy the template and edit it:

```bash
cp config/tpl.config.toml config/config.toml
```

Fill in the following placeholders in `config.toml`:

| Placeholder | Description |
|-------------|-------------|
| `<gitlab-url>` | URL of your GitLab instance (e.g. `https://gitlab.com`). |
| `<gitlab-token>` | Registration token from step 1. |
| `<minio-server-url>` | Address of the MinIO/S3 server for job cache (e.g. `vps.example.com:9000`). |
| `<key-name>` | S3 access key for the cache bucket. |
| `<key-secret>` | S3 secret key for the cache bucket. |
| `<bucket-name-for-cache>` | S3 bucket name used for the shared cache. |
| `<private-or-global-image-name>` | Default Docker image for job containers. |

### 3. Preserve the runner system ID

If you previously ran this runner and want to keep the same entry in GitLab's runner list, copy the existing `System-ID` value into `config/.runner_system_id`. Without it, each restart may register a new runner entry.

## Using the Helper Script

The `gitlab-runner.sh` script simplifies managing the runner container. Make it executable first:

```bash
chmod +x gitlab-runner.sh
```

### Commands

| Command | Description |
|---------|-------------|
| `register` | Register the runner with GitLab (requires `-t` / `--token` option). |
| `run` | Run the runner interactively with debug logging. |
| `start` | Run the runner in the background (detached mode). |
| `stop` | Stop a background running runner. |
| `kill` | Kill a background running runner. |
| `status` | Check the runner status. |
| `list` | List registered runners. |
| `attach` | Attach to the runner container's console. |

### Options

| Option | Description |
|--------|-------------|
| `-t`, `--token` | GitLab runner registration token. |
| `-h`, `--help` | Show help message. |
| `--pause` | Pause after execution (wait for key press). |

### Examples

Register the runner:

```bash
./gitlab-runner.sh -t "your-registration-token" register
```

Run the runner in the background:

```bash
./gitlab-runner.sh start
```

Run the runner interactively with debug logs:

```bash
./gitlab-runner.sh run
```

Stop the runner:

```bash
./gitlab-runner.sh stop
```

## Configuration Details

### Docker executor

The runner uses the `docker` executor. Key settings in `config.toml`:

- **`privileged: true`** — Required for features like FUSE inside job containers.
- **`pull_policy: "always"`** — Always pull the latest version of the job image.
- **`volumes: ["/cache"]`** — Local cache volume mounted into job containers.

### S3 / MinIO cache

The runner stores build artifacts in a shared S3-compatible cache (MinIO). Configure the `[runners.cache.s3]` section with your server address, credentials, and bucket name. Set `Insecure = true` if the server uses self-signed certificates.

## Related

- **[GitLab CI Pipeline](../../gitlab-ci/README.md)** — Pipeline configuration, environments, and CI/CD variables for the GitLab side.
