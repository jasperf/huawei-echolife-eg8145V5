# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a documentation repository for managing the Huawei EchoLife EG8145V5 Optical Network Terminal (ONT). It contains no code - only documentation about device management, CLI commands, and configuration guides.

## Repository Structure

```
.
├── README.md                    # Main overview and quick start guide
└── docs/
    ├── access-methods.md       # SSH, web, telnet access and authentication
    ├── command-reference.md    # Comprehensive CLI command reference
    ├── security.md             # Security hardening and best practices
    └── troubleshooting.md      # Common issues and solutions
```

## Repository Purpose

The repository serves as a knowledge base and reference guide for:
- SSH/CLI access to the EG8145V5 device
- Device configuration and management commands
- Network troubleshooting procedures
- Security best practices for the ONT device

## Key Information

**Device Access (Indonesian ISP):**
- Default IP: `192.168.18.1`
- SSH user: `Eproot` (not `root` - ISP-specific)
- Web user: `Epadmin`
- SSH requires legacy algorithms: `ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa Eproot@192.168.18.1`
- After login, elevate with `su` to access `SU_WAP>` mode for administrative commands
- Always save changes with `save data`

**Important Context:**
- This is network hardware documentation, not software development
- The EG8145V5 is ISP-managed equipment with proprietary Huawei CLI
- SSH-RSA is legacy but required by the device hardware
- Firmware updates are typically ISP-controlled via TR-069
- Credentials vary by ISP: Indonesian ISP uses `Eproot`/`Epadmin`

## Common Tasks

Since this is a documentation repository, typical tasks involve:

1. **Updating Documentation:**
   - Edit [README.md](README.md) for quick start and overview content
   - Edit detailed guides in [docs/](docs/) directory for in-depth information
   - Ensure command syntax matches the device's CLI interface
   - Include real-world examples and expected outputs

2. **Adding New Sections:**
   - README.md should remain concise with links to detailed docs
   - Add detailed content to appropriate file in docs/ directory
   - Use consistent formatting for command examples
   - Include context about when/why commands are used

3. **Organizing Content:**
   - Access and authentication → [docs/access-methods.md](docs/access-methods.md)
   - CLI commands and reference → [docs/command-reference.md](docs/command-reference.md)
   - Security topics → [docs/security.md](docs/security.md)
   - Troubleshooting → [docs/troubleshooting.md](docs/troubleshooting.md)
   - Quick reference → [README.md](README.md)

4. **Security Context:**
   - This repository documents authorized access to network equipment
   - All commands assume legitimate device ownership/administration
   - The legacy SSH algorithms are device limitations, not security choices
   - Always emphasize security best practices (password changes, etc.)
