# Access Methods

This document covers the various methods to access and authenticate with the Huawei EchoLife EG8145V5.

## SSH Access

The device supports SSH access with legacy key algorithms:

```bash
# For Indonesian ISP
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa Eproot@192.168.18.1

# For other ISPs (replace username as needed)
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.18.1
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa admin@192.168.18.1
```

### Default Credentials (ISP-Specific)

**⚠️ IMPORTANT:** Default usernames vary by ISP. Each ISP configures their devices differently.

#### Common Credential Variations

| ISP/Region | SSH/CLI Username | Web GUI Username | Notes |
|------------|------------------|------------------|-------|
| **Indonesian ISP** | `Eproot` | `Epadmin` | Verified - starts with "Ep" prefix |
| Generic/Default | `root` | `admin` | Most common default |
| Some ISPs | `admin` | `admin` | Alternative configuration |
| telecomadmin | `telecomadmin` | `telecomadmin` | Some European ISPs |
| User-created | `user` | `user` | Rare, some ISPs allow custom |

**How to Find Your Credentials:**
1. Check documentation from your ISP
2. Look for sticker on the device (may show username)
3. Contact ISP technical support
4. Try common defaults: `root`, `admin`, `Eproot`

**For Indonesian ISP (Example):**
- **SSH/CLI:** Username: `Eproot`, Password: (ISP-provided)
- **Web Interface:** Username: `Epadmin`, Password: (ISP-provided)

**For Generic/Default:**
- **SSH/CLI:** Username: `root`, Password: (ISP-provided)
- **Web Interface:** Username: `admin`, Password: (ISP-provided)

**Security Note:** Regardless of ISP, always change default passwords immediately!

### SSH Configuration Notes

The device uses older SSH key algorithms (SSH-RSA), which are considered legacy:

```bash
# Required SSH options
-o HostKeyAlgorithms=+ssh-rsa
-o PubkeyAcceptedKeyTypes=+ssh-rsa
```

**Security Note:** SSH-RSA is deprecated in modern SSH implementations. Use this device behind a secure network and consider:
- Keeping it on an isolated VLAN
- Using firewall rules to restrict management access
- Regular password updates

### Initial Login Workflow

1. Connect via SSH (replace username with your ISP's default):
   ```bash
   # Indonesian ISP
   ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa Eproot@192.168.18.1

   # Generic/Other ISPs
   ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.18.1
   ```

2. You'll be in the WAP shell (limited access):
   ```
   WAP>
   ```

3. Elevate to super user mode:
   ```
   WAP> su
   ```

4. You're now in privileged mode:
   ```
   SU_WAP>
   ```

5. Change the default password (CRITICAL):
   ```
   SU_WAP> set userpasswd
   ```

6. Save your changes:
   ```
   SU_WAP> save data
   ```

## Web Interface

Access the web management interface at:

```
http://192.168.18.1
```

**Default Credentials (ISP-Specific):**

| ISP/Region | Username | Password |
|------------|----------|----------|
| Indonesian ISP | `Epadmin` | (ISP-provided) |
| Generic/Default | `admin` | (ISP-provided) |
| Alternative | `admin` | `admin` |
| Some ISPs | `telecomadmin` | (ISP-provided) |

**If Unsure:** Try these usernames in order:
1. Check ISP documentation first
2. `Epadmin` (Indonesian ISP)
3. `admin` (most common)
4. `root`
5. `telecomadmin`

**Features:**
- Graphical interface for configuration
- Network topology visualization
- WiFi settings management
- Port forwarding configuration
- Firmware updates (ISP-controlled)
- System logs and diagnostics

**Limitations:**
- Some advanced features only available via CLI
- TR-069 management may restrict certain options
- ISP may lock down certain configurations

## Telnet Access

Telnet may be available but is **not recommended** due to security concerns:
- Unencrypted transmission
- Credentials sent in plaintext
- No protection against man-in-the-middle attacks

**If you must use Telnet:**

```bash
telnet 192.168.18.1
# Login with same credentials as SSH (Eproot for Indonesian ISP, root for others)
```

**Recommendation:** Always prefer SSH over Telnet. If Telnet is enabled and not needed, disable it:

```
SU_WAP> set aptelnet
```

## Shell Access

From the SU_WAP prompt, you can access a limited Linux shell:

```
SU_WAP> shell
```

**Capabilities:**
- Standard Linux commands (ls, cat, grep, etc.)
- System file access (limited)
- Process management
- Network diagnostics

**Exit shell:**
```bash
exit
```

## Access Modes Summary

| Mode | Prompt | Access Method | Capabilities |
|------|--------|---------------|--------------|
| WAP | `WAP>` | Initial SSH login | Basic commands only |
| SU_WAP | `SU_WAP>` | `su` from WAP | Full administrative access |
| Shell | `#` or `$` | `shell` from SU_WAP | Linux command line |
| Web | N/A | HTTP browser | Graphical configuration |

## Authentication Best Practices

1. **Change Default Passwords Immediately**
   ```
   SU_WAP> set userpasswd
   ```

2. **Use Strong Passwords**
   - Minimum 12 characters
   - Mix of uppercase, lowercase, numbers, symbols
   - Avoid dictionary words

3. **Disable Unused Services**
   - Disable Telnet if not needed
   - Disable remote management if not used

4. **Regular Password Rotation**
   - Change passwords every 90 days
   - Don't reuse old passwords

5. **Limit Access**
   - Use firewall rules to restrict management access
   - Consider MAC address filtering
   - Disable WAN-side management if not needed

## Troubleshooting Access Issues

### Cannot Connect via SSH

1. Verify network connectivity:
   ```bash
   ping 192.168.18.1
   ```

2. Check SSH is enabled on the device

3. Verify you're using correct SSH options:
   ```bash
   ssh -v -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa Eproot@192.168.18.1
   ```

4. Try from a different client or network

### Incorrect Password

1. If you've forgotten the password, you may need to:
   - Contact your ISP for reset
   - Perform factory reset (will erase all settings)

2. Factory reset procedure:
   - Hardware reset button (hold 10+ seconds)
   - Or via CLI: `restore manufactory`

### Web Interface Not Loading

1. Check browser compatibility (use modern browser)

2. Clear browser cache

3. Try different browser or incognito mode

4. Verify HTTP (not HTTPS):
   ```
   http://192.168.18.1
   ```

5. Check if web service is enabled on device
