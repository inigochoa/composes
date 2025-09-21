# Composes

A complete self-hosted infrastructure and service deployment using Docker
Compose.

## Overview

This repository contains all the necessary configurations to deploy a complete
stack of self-hosted services managed by Docker Compose.

## Requirements

- Docker Engine (version 20.10+).
- Docker Compose V2.
- Git.
- At least 4GB RAM (recommended 8GB+).
- Sufficient disk space for your services.

## Services

- **[Diun]**: Monitor Docker image updates

## Data Storage

All service data is stored in `/opt/data/[service-name]`. Make sure this
directory exists and has appropriate permissions.

## Getting Started

1. Clone this repository:
   ```bash
   git clone https://github.com/inigochoa/composes.git
   cd composes
   ```

1. Prepare a service:
   ```bash
   cd [service-name]
   cp -p [service-name]/.env.example [service-name]/.env
   ```

1. Edit the generated `.env` files with your specific configuration values.

1. Deploy the service:
   ```bash
   docker compose up -d
   ```

## Contributing

1. Fork the repository
1. Create your feature branch
1. Commit your changes
1. Push to the branch
1. Create a new Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE] file for
details.

[Diun]: ./diun/README.md
[LICENSE]: ./LICENSE
