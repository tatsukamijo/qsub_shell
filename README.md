# qsub_shell - ABCI Interactive Shell Launcher

A convenient command-line tool for launching interactive shell sessions on [ABCI](https://abci.ai/en/) (AI Bridging Cloud Infrastructure) HPC systems using the PBS job scheduler.

## Overview

`qsub_shell` simplifies the process of requesting interactive computing resources on ABCI by providing an intuitive interface to the `qsub` command. It automatically handles queue selection, resource specification, and time formatting while offering sensible defaults for common use cases.

## Features

- **GPU Resource Management**: Easy specification of GPU counts with automatic queue selection
- **Flexible Resource Allocation**: Configurable CPU cores, memory, and time limits
- **Smart Queue Selection**: Automatically chooses appropriate queues based on resource requirements
- **Time Format Conversion**: Converts minutes to HH:MM:SS format automatically
- **Comprehensive Help**: Built-in help system with examples and resource type information

## Installation

1. Clone this repository or download the `qsub_shell` script
2. Change the group name in the script to your project group name
3. Make the script executable:
   ```bash
   chmod +x qsub_shell
   ```
3. Move to your PATH or use with relative path:
   ```bash
   ./qsub_shell [options]
   ```

## Usage

### Basic Syntax
```bash
qsub_shell [OPTIONS]
```

### Options

| Option | Long Option | Description | Default |
|--------|-------------|-------------|---------|
| `-g` | `--gres gpu:N` | Number of GPUs (use 0 for CPU-only) | 1 |
| `--no-gpu` | | CPU-only mode | false |
| `-t` | `--time MINUTES` | Time limit in minutes | 30 |
| `-m` | `--mem GB` | Memory in GB | 8 |
| `-c` | `--cores N` | Number of CPU cores | 1 |
| `-p` | `--project NAME` | Project group name | gcf51142 |
| `-q` | `--queue NAME` | Queue name (auto-selected) | rt_HG/rt_HC |
| `-n` | `--name NAME` | Job name | interactive |
| `-h` | `--help` | Show help information | |

### Examples

```bash
# Default: 1 GPU, 30 minutes
qsub_shell
# Executes: qsub -I -P gcf51142 -q rt_HG -l select=1 -l walltime=00:30:00 -N interactive

# 2 GPUs, 60 minutes
qsub_shell -g 2 -t 60
# Executes: qsub -I -P gcf51142 -q rt_HG -l select=1 -l walltime=01:00:00 -N interactive

# 4 GPUs, 120 minutes
qsub_shell --gres gpu:4 --time 120
# Executes: qsub -I -P gcf51142 -q rt_AG -l select=1 -l walltime=02:00:00 -N interactive -l gpu=4

# CPU-only mode
qsub_shell --no-gpu
# Executes: qsub -I -P gcf51142 -q rt_HC -l select=1 -l walltime=00:30:00 -N interactive

# CPU-only mode (alternative)
qsub_shell -g 0
# Executes: qsub -I -P gcf51142 -q rt_HC -l select=1 -l walltime=00:30:00 -N interactive

# Custom memory and CPU cores
qsub_shell -m 16 -c 4
# Executes: qsub -I -P gcf51142 -q rt_HG -l select=1 -l walltime=00:30:00 -N interactive
# Note: -m and -c options are parsed but not yet implemented in qsub command

# Custom project and job name
qsub_shell -p myproject -n myjob
# Executes: qsub -I -P myproject -q rt_HG -l select=1 -l walltime=00:30:00 -N myjob
```

## Resource Types

The script automatically selects appropriate queues based on your requirements:

| Queue | GPU | CPU | Memory | Use Case |
|-------|-----|-----|--------|----------|
| `rt_HC` | 0 | 32 | 320GB | CPU-intensive tasks |
| `rt_HG` | 1 | 16 | 160GB | Single GPU workloads |
| `rt_AG` | 4 | 64 | 512GB | Multi-GPU training |
| `rt_G` | 8 | 128 | 1024GB | Large-scale distributed training |

## How It Works

1. **Input Validation**: Validates all parameters for correctness
2. **Queue Selection**: Automatically chooses the appropriate queue based on GPU count
3. **Time Formatting**: Converts minutes to HH:MM:SS format required by PBS
4. **Command Construction**: Builds the appropriate `qsub` command with all parameters
5. **Execution**: Launches the interactive job using `exec`

## Requirements

- ABCI account with PBS job scheduler access
- Bash shell environment
- Appropriate project group permissions

## Error Handling

The script includes comprehensive error checking:
- Parameter validation with descriptive error messages
- Resource constraint validation
- Graceful exit on invalid inputs

## Contributing

Feel free to submit issues, feature requests, or pull requests to improve this tool.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
