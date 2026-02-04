# Contributing to MinnowVPN

Thank you for your interest in contributing to MinnowVPN! This guide covers contributions to the **deployment repository** (Docker Compose, scripts, and configuration).

> **Note:** For contributions to the VPN daemon itself (Rust code), see the [minnowvpn-service repository](https://github.com/minnowvpn/minnowvpn-service).

## Getting Started

### Prerequisites

- Docker Engine 20.10+
- Docker Compose 2.0+
- A Linux, macOS, or Windows machine for testing

### Local Development Setup

1. Fork and clone the repository:
   ```bash
   git clone https://github.com/YOUR_USERNAME/minnowvpn.git
   cd minnowvpn
   ```

2. Copy the example environment file:
   ```bash
   cp .env.example .env
   ```

3. Edit `.env` with your settings (use a test domain or localhost)

4. Start the services:
   ```bash
   docker compose up -d
   ```

5. Check service health:
   ```bash
   docker compose ps
   docker compose logs -f
   ```

## What to Contribute

We welcome contributions in these areas:

### High Priority
- Bug fixes for setup scripts
- Documentation improvements
- Support for additional Linux distributions
- Monitoring and alerting improvements

### Medium Priority
- Docker Compose optimizations
- Caddy configuration enhancements
- Fail2ban rule improvements
- Backup/restore script enhancements

### Ideas Welcome
- CI/CD pipeline improvements
- Alternative deployment methods (Kubernetes, etc.)
- Additional SSO provider configurations

## Contribution Guidelines

### Code Style

- **Shell scripts**: Use `#!/bin/bash`, quote variables, use `set -e`
- **YAML files**: 2-space indentation, no tabs
- **Docker Compose**: Follow existing patterns for service definitions

### Commit Messages

Use clear, descriptive commit messages:

```
Add health check for dart-server container

- Add healthcheck directive to docker-compose.yml
- Set appropriate interval and timeout values
- Update README with health check documentation
```

### Testing Requirements

Before submitting a PR:

1. **Fresh deployment test**: Test with a clean `docker compose down -v && docker compose up -d`
2. **Service health**: Verify all services reach "healthy" status
3. **Basic functionality**: Create an admin account, add a client, connect
4. **Architecture**: Test on both amd64 and arm64 if possible

### Pull Request Process

1. Create a feature branch: `git checkout -b feature/my-improvement`
2. Make your changes with clear commits
3. Test thoroughly (see above)
4. Push and create a Pull Request
5. Fill out the PR template completely
6. Wait for review and address feedback

## Security

- **Never commit secrets** - Use environment variables or Docker secrets
- **Sanitize examples** - Replace real values with placeholders
- **Report vulnerabilities privately** - See [SECURITY.md](SECURITY.md)

## Questions?

- Open a [Discussion](https://github.com/minnowvpn/minnowvpn/discussions) for questions
- Check existing [Issues](https://github.com/minnowvpn/minnowvpn/issues) before creating new ones

## License

By contributing, you agree that your contributions will be licensed under the Apache License 2.0.
