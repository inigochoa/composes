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
- **[Duplicati]**: Backup data
- **[FreshRSS]**: A RSS and Atom feed aggregator
- **[Joplin]**: An open-source note-taking and to-do application
- **[Mealie]**: A recipe manager and meal planner
- **[Newt]**: Docker container management for [Pangolin]
- **[Syncthing]**: A continuous file synchronization program
- **[Vaultwarden]**: an unofficial [Bitwarden] compatible server written in Rust
- **[Wallabag]**: A read-it-later application to save web articles
- **[Warracker]**: A warranty tracker
- **[Yamtrack]**: A media tracking application

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

[Bitwarden]: https://bitwarden.com/
[Diun]: ./diun/README.md
[Duplicati]: ./duplicati/README.md
[FreshRSS]: ./freshrss/README.md
[Mealie]: ./mealie/README.md
[Joplin]: ./joplin/README.md
[LICENSE]: ./LICENSE
[Newt]: ./newt/README.md
[Pangolin]: https://docs.digpangolin.com/
[Syncthing]: ./syncthing/README.md
[Vaultwarden]: ./vaultwarden/README.md
[Wallabag]: ./wallabag/README.md
[Warracker]: ./warracker/README.md
[Yamtrack]: ./yamtrack/README.md
