# Huawei EchoLife EG8145V5 Management Guide

Comprehensive documentation and tools for managing the Huawei EchoLife EG8145V5 Optical Network Terminal (ONT).

## Overview

The Huawei EchoLife EG8145V5 is a Gigabit-capable Passive Optical Network (GPON) Optical Network Terminal designed for FTTH (Fiber To The Home) deployments. It provides high-speed internet access, VoIP telephony, and IPTV services over fiber optic networks.

**Key Features:**
- GPON interface supporting up to 2.5 Gbps downstream and 1.25 Gbps upstream
- 4x Gigabit Ethernet LAN ports
- Dual-band WiFi (2.4GHz and 5GHz) - 802.11ac/n/b/g
- 2x VoIP (POTS) ports
- USB ports for storage/sharing
- IPTV/CATV capabilities

## Quick Start

### Access the Device

**SSH Access (Recommended):**
```bash
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa Eproot@192.168.18.1
```

**Web Interface:**
```
http://192.168.18.1
Username: Epadmin
Password: (ISP-provided)
```

### Indonesian ISP Default Credentials

**SSH/CLI:**
- Username: `Eproot`
- Password: (ISP-provided default)

**Web Interface:**
- Username: `Epadmin`
- Password: (ISP-provided default)

**⚠️ IMPORTANT:** Change default passwords immediately after first login!

### Basic Command Flow

```bash
# Connect via SSH
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa Eproot@192.168.18.1

# Elevate to super user mode
WAP> su

# You're now in administrative mode
SU_WAP>

# Change password (recommended)
SU_WAP> set userpasswd

# View device information
SU_WAP> display deviceInfo
SU_WAP> display onu info
SU_WAP> display optic

# Always save configuration changes
SU_WAP> save data
```

## Documentation

Detailed documentation is organized in the [`docs/`](docs/) directory:

- **[Access Methods](docs/access-methods.md)** - SSH, web interface, authentication, and access modes
- **[Command Reference](docs/command-reference.md)** - Comprehensive CLI command guide organized by category
- **[Security Guide](docs/security.md)** - Security best practices, hardening, and authentication
- **[Troubleshooting](docs/troubleshooting.md)** - Common issues and solutions

## Essential Commands

### System Information
```bash
display deviceInfo          # Device information
display onu info           # ONT registration status
display optic              # Fiber signal quality
display version            # Software version
display cpu info           # CPU usage
display memory info        # Memory usage
```

### Network Status
```bash
display waninfo all        # WAN connection status
display ip route           # Routing table
display dhcp server user all  # Connected devices
ping <ip>                  # Test connectivity
```

### WiFi Management
```bash
display wifi information   # WiFi status
display wifi associate     # Connected WiFi clients
get wlan basic            # WiFi configuration
set wlan enable           # Enable/disable WiFi
set ssid                  # Change SSID/password
```

### Configuration
```bash
save data                 # Save configuration (CRITICAL!)
display current-configuration  # View config
restore manufactory       # Factory reset
reset                     # Reboot device
```

## Common Tasks

### Check Device Status
```bash
SU_WAP> display deviceInfo
SU_WAP> display onu info
SU_WAP> display optic
```

### Check Fiber Signal Quality
```bash
SU_WAP> display optic
```
- **Good:** RX power -8 to -28 dBm
- **Marginal:** -28 to -30 dBm
- **Poor:** Below -30 dBm

### View Connected Devices
```bash
SU_WAP> display wifi associate
SU_WAP> display dhcp server user all
```

### Change WiFi Settings
```bash
SU_WAP> set ssid
# Follow prompts
SU_WAP> save data
```

### Monitor Bandwidth
```bash
SU_WAP> display port statistics
SU_WAP> display pon statistics
```

## Security Considerations

**Critical Security Steps:**

1. **Change Default Passwords Immediately**
   ```bash
   SU_WAP> set userpasswd
   ```

2. **Disable Telnet (Use SSH Only)**
   ```bash
   SU_WAP> set aptelnet
   ```

3. **Use Strong WiFi Security**
   - WPA2-PSK (AES) or WPA3
   - Strong password (12+ characters)
   - Change default SSID

4. **Enable Firewall**
   ```bash
   SU_WAP> display firewall rule
   ```

5. **Disable WPS if Not Needed**

See [Security Guide](docs/security.md) for comprehensive security hardening.

## Troubleshooting Quick Reference

### No Internet
1. Check fiber: `display optic` (RX power should be -8 to -28 dBm)
2. Check ONT: `display onu info` (should show "Registered")
3. Check WAN: `display waninfo all`
4. Test: `ping 8.8.8.8`

### WiFi Issues
1. Check radio: `display wifi radio`
2. Check config: `get wlan basic`
3. Check clients: `display wifi associate`

### Performance Issues
1. Check optics: `display optic`
2. Check resources: `display cpu info`, `display memory info`
3. Check stats: `display pon statistics`

See [Troubleshooting Guide](docs/troubleshooting.md) for detailed solutions.

## Device Specifications

**Official Documentation:** [Huawei Enterprise Support](https://support.huawei.com/enterprise/en/optical-access/echolife-eg8145v5-pid-23112449)

**Network Interfaces:**
- 1x GPON port (SC/APC connector)
- 4x GE LAN ports (10/100/1000 Mbps)
- 2x POTS (voice) ports
- 1-2x USB 2.0 ports
- WiFi 802.11ac (dual-band 2.4GHz/5GHz)

**Performance:**
- Downstream: Up to 2.5 Gbps (GPON)
- Upstream: Up to 1.25 Gbps (GPON)
- WiFi: Up to 1.2 Gbps (802.11ac)

## Command Categories

The EG8145V5 CLI provides hundreds of commands organized into categories:

- **System Management:** version, sysinfo, cpu info, memory info
- **Network Configuration:** ip interface, dhcp, routing, VLAN
- **WiFi Management:** wlan config, wifi filter, associated clients
- **GPON/ONT:** onu info, optical transceiver, pon statistics
- **VoIP:** voip info, dsp status, call logs, SIP registration
- **Security:** firewall, NAT, port forwarding, MAC filtering
- **Diagnostics:** ping, traceroute, port statistics, system logs
- **OMCI/TR-069:** Device management and ISP provisioning

See [Command Reference](docs/command-reference.md) for complete details.

## Important Notes

### SSH Legacy Algorithms

The device requires legacy SSH algorithms:
```bash
-o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa
```

**Security Note:** SSH-RSA is deprecated in modern SSH clients. Use the device behind a secure network and:
- Keep it on an isolated VLAN
- Use strong passwords
- Regular password rotation
- Disable WAN-side management

### ISP Management (TR-069)

The device is typically managed by your ISP via TR-069/CWMP:
- Firmware updates are ISP-controlled
- Some settings may be locked or overridden
- Factory reset may require ISP reprovisioning

**Check TR-069 Status:**
```bash
SU_WAP> display cwmp status
SU_WAP> display tr069 info
```

### Configuration Management

**Always save after changes:**
```bash
SU_WAP> save data
```

Unsaved changes will be lost on reboot!

## Resources

- [Official Huawei Support](https://support.huawei.com/enterprise/en/optical-access/echolife-eg8145v5-pid-23112449)
- [Access Methods Guide](docs/access-methods.md)
- [Command Reference](docs/command-reference.md)
- [Security Guide](docs/security.md)
- [Troubleshooting Guide](docs/troubleshooting.md)

## Contributing

Contributions are welcome! If you have additional tips, scripts, or documentation for managing the EG8145V5, please feel free to contribute.

**Areas for Contribution:**
- Additional command examples
- ISP-specific configurations
- Troubleshooting scenarios
- Automation scripts
- Performance optimization tips

## Disclaimer

This documentation is provided for informational and educational purposes. Always follow your ISP's terms of service and local regulations when configuring network equipment. Modifying certain settings may violate your service agreement or cause service interruptions.

**Use at your own risk.** The authors are not responsible for any damage or service issues resulting from following this guide.

## License

This documentation is provided as-is for community use.

---

**Last Updated:** November 2025
**ISP Context:** Indonesian ISP (Eproot/Epadmin credentials)
**Default Gateway:** 192.168.18.1
