# Security Guide

Security best practices and hardening guide for the Huawei EchoLife EG8145V5.

## Critical Security Steps

These steps should be performed immediately after initial setup:

### 1. Change Default Passwords

**SSH/CLI Password:**
```bash
SU_WAP> set userpasswd
```

Follow the prompts to set a new password.

**Web Interface:**
Access http://192.168.18.1 with username `Epadmin` and change password through the web interface.

**Password Requirements:**
- Minimum 12 characters
- Mix of uppercase and lowercase letters
- Include numbers
- Include special characters
- Avoid dictionary words
- Don't reuse passwords from other devices

### 2. Disable Unnecessary Services

**Disable Telnet:**
```bash
SU_WAP> set aptelnet
SU_WAP> save data
```

Telnet transmits credentials in plaintext. Use SSH only.

**Check Service Status:**
```bash
SU_WAP> display service status
```

### 3. Configure Strong WiFi Security

**Check Current WiFi Security:**
```bash
SU_WAP> get wlan basic
SU_WAP> get wlan advance
```

**WiFi Security Checklist:**
- [ ] Use WPA2-PSK (AES) or WPA3
- [ ] Set strong WiFi password (12+ characters)
- [ ] Change default SSID
- [ ] Disable SSID broadcast (optional, for advanced users)
- [ ] Disable WPS if not needed
- [ ] Enable MAC address filtering (optional)

**Configure WiFi Security:**
```bash
SU_WAP> set wlan basic
SU_WAP> set ssid
SU_WAP> save data
```

### 4. Enable and Configure Firewall

**Check Firewall Status:**
```bash
SU_WAP> display firewall status
SU_WAP> display firewall rule
```

**Firewall Best Practices:**
- Enable stateful packet inspection
- Block incoming WAN connections by default
- Only allow required ports
- Use explicit allow rules rather than deny rules

### 5. Review and Secure Remote Management

**Disable WAN-side Management:**
Most users don't need WAN-side management access. Ensure management is only accessible from LAN.

**Check Access Control:**
```bash
SU_WAP> display acl
```

## SSH Security

### Legacy Algorithm Issue

The EG8145V5 uses SSH-RSA, which is deprecated:

```bash
# For Indonesian ISP
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa Eproot@192.168.18.1

# For other ISPs (replace username)
ssh -o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa root@192.168.18.1
```

**Why This Matters:**
- SSH-RSA is considered cryptographically weak by modern standards
- Newer SSH clients disable it by default
- Potential vulnerability to advanced attacks

**Mitigation Strategies:**

1. **Network Isolation:**
   - Keep device on isolated VLAN
   - Use firewall rules to restrict management access
   - Only allow SSH from trusted IPs

2. **Access Control:**
   - Don't expose SSH to WAN
   - Use VPN for remote management
   - Consider jump host for SSH access

3. **Monitoring:**
   - Regularly check system logs for unauthorized access attempts
   - Monitor failed login attempts

4. **Defense in Depth:**
   - Strong passwords
   - Regular password rotation
   - Limit access to known devices

## Firewall Configuration

### Basic Firewall Rules

**View Current Rules:**
```bash
SU_WAP> display firewall rule
```

**Best Practices:**
1. Default deny inbound from WAN
2. Allow established/related connections
3. Explicit allow rules for required services
4. Drop invalid packets
5. Rate limiting for DoS protection

### Port Forwarding Security

**View Port Mappings:**
```bash
SU_WAP> display nat port mapping
```

**Port Forwarding Best Practices:**
1. Only forward required ports
2. Use non-standard ports when possible
3. Limit source IPs if possible
4. Document all port forwards
5. Regularly review and remove unused forwards
6. Use VPN instead when possible

**Example Use Cases:**
- Port 80/443: Web server (consider Cloudflare/reverse proxy)
- Port 22: SSH (use non-standard port, key auth, fail2ban)
- Port 3389: RDP (highly discouraged, use VPN instead)

## MAC Address Filtering

### Enable MAC Filtering

**View Current MAC Filters:**
```bash
SU_WAP> display lan mac filter
```

**Add Allowed MAC:**
```bash
SU_WAP> lan mac filter add
```

**Enable MAC Filtering:**
```bash
SU_WAP> lan mac filter enable
SU_WAP> save data
```

**Pros:**
- Additional layer of security
- Prevents unknown devices from connecting

**Cons:**
- Can be inconvenient for guest access
- MAC addresses can be spoofed
- Not a substitute for strong encryption

**Best Practices:**
- Use in combination with WPA2/WPA3
- Maintain list of authorized MACs
- Regularly review allowed devices

## WiFi Security

### WPA2 vs WPA3

**WPA2-PSK (AES):**
- Widely supported
- Secure for most use cases
- Minimum recommended security level

**WPA3:**
- Latest security standard
- Enhanced protection
- May have compatibility issues with older devices

**Check WPA Version:**
```bash
SU_WAP> get wlan basic
```

### WiFi Best Practices

1. **SSID Security:**
   - Change default SSID
   - Don't use personally identifiable information
   - Consider hiding SSID (optional)

2. **Password Security:**
   - Minimum 12 characters
   - Random characters preferred
   - Change periodically (every 6-12 months)
   - Don't share widely

3. **Guest Network:**
   - Use separate guest SSID if supported
   - Isolate guest network from main network
   - Different password than main network

4. **Disable WPS:**
   ```bash
   SU_WAP> get wlan wps
   ```

   WPS has known vulnerabilities. Disable if not needed.

### WiFi Monitoring

**Monitor Connected Devices:**
```bash
SU_WAP> display wifi associate
SU_WAP> get wlan associated
```

**Regular Checks:**
- Review connected devices weekly
- Investigate unknown MAC addresses
- Disconnect unauthorized devices

## VoIP Security

### SIP Security Considerations

**Check VoIP Configuration:**
```bash
SU_WAP> display voip info
```

**VoIP Security Risks:**
- Eavesdropping on calls
- Toll fraud
- Denial of service

**Mitigation:**
1. Use SIP over TLS if supported
2. Strong SIP passwords
3. Disable VoIP if not used
4. Monitor call logs for unusual activity
5. Consider SRTP for encrypted media

**Monitor Call Logs:**
```bash
SU_WAP> display last call log
```

## Network Segmentation

### VLAN Configuration

If your network supports VLANs:

1. **Management VLAN:**
   - Dedicated VLAN for device management
   - Restricted access

2. **User VLANs:**
   - Separate trusted devices
   - Guest network
   - IoT devices

3. **Service VLANs:**
   - VoIP (QoS priority)
   - IPTV

**Benefits:**
- Isolation of traffic
- Better security
- QoS management
- Broadcast domain separation

## Monitoring and Logging

### System Logs

**View System Logs:**
```bash
SU_WAP> display syslog
SU_WAP> display log info
```

**What to Monitor:**
- Failed login attempts
- Configuration changes
- Network anomalies
- Service restarts
- WAN disconnections

### Security Monitoring

**Regular Checks:**

1. **Weekly:**
   - Review connected WiFi devices
   - Check system logs
   - Verify firewall rules

2. **Monthly:**
   - Change WiFi password (optional)
   - Review port forwards
   - Check for firmware updates

3. **Quarterly:**
   - Change admin passwords
   - Audit all security settings
   - Review and update MAC filters

### Alarm Monitoring

**Check Alarms:**
```bash
SU_WAP> display alarm current
SU_WAP> display alarm history
```

## Firmware Updates

### ISP-Managed Updates

The EG8145V5 is typically managed by ISP via TR-069:

**Check TR-069 Status:**
```bash
SU_WAP> display cwmp status
SU_WAP> display tr069 info
```

**Check Current Version:**
```bash
SU_WAP> display version
```

**Update Considerations:**
1. Updates usually automatic from ISP
2. Manual updates may be restricted
3. Don't disable TR-069 without ISP approval
4. Backup configuration before updates

## Backup and Recovery

### Configuration Backup

**Save Configuration:**
```bash
SU_WAP> save data
```

**View Backup List:**
```bash
SU_WAP> display backup list
```

**Best Practices:**
1. Save after every configuration change
2. Document all changes
3. Keep offline copy of critical settings
4. Test restore procedure

### Disaster Recovery

**Scenarios to Plan For:**
1. Device failure → Replacement device
2. Configuration corruption → Factory reset + restore
3. Compromised device → Factory reset + reconfigure
4. ISP settings lost → Contact ISP for reprovisioning

**Recovery Steps:**
```bash
# Factory reset
SU_WAP> restore manufactory

# Device will reboot and require reconfiguration
```

## ISP-Specific Considerations

### Default Credentials

**⚠️ IMPORTANT:** Default credentials vary by ISP. Each ISP configures devices differently.

#### Common Credential Variations

| ISP/Region | SSH/CLI Username | Web GUI Username |
|------------|------------------|------------------|
| **Indonesian ISP** | `Eproot` | `Epadmin` |
| Generic/Default | `root` | `admin` |
| Some ISPs | `admin` | `admin` |
| European ISPs | `telecomadmin` | `telecomadmin` |

**For Indonesian ISP:**
- **SSH/CLI:** Username: `Eproot`, Password: (ISP-provided)
- **Web Interface:** Username: `Epadmin`, Password: (ISP-provided)

**For Generic/Default:**
- **SSH/CLI:** Username: `root`, Password: (ISP-provided)
- **Web Interface:** Username: `admin`, Password: (ISP-provided)

**Important:**
- Change these immediately after first login regardless of ISP
- ISP may have ability to reset via TR-069
- Contact your ISP if you're unsure of default credentials

### TR-069 Management

Most ISPs (including Indonesian providers) use TR-069 for:
- Remote configuration
- Firmware updates
- Service provisioning
- Diagnostics

**Security Implications:**
- ISP has remote access to device
- Configuration may be overwritten by ISP
- Some settings may be locked

**What You Control:**
- Local admin passwords
- WiFi settings (usually)
- Firewall rules (usually)
- Port forwarding (usually)

**What ISP Controls:**
- WAN configuration
- VLAN settings
- Service parameters
- Firmware versions

## Security Checklist

Use this checklist to verify your security posture:

- [ ] Changed default SSH/CLI password (Eproot for Indonesian ISP, root for others)
- [ ] Changed default web interface password (Epadmin for Indonesian ISP, admin for others)
- [ ] Disabled Telnet service
- [ ] Enabled WPA2/WPA3 on WiFi
- [ ] Set strong WiFi password (12+ characters)
- [ ] Changed default SSID
- [ ] Disabled WPS
- [ ] Enabled firewall
- [ ] Reviewed and secured port forwards
- [ ] Disabled WAN-side management
- [ ] Enabled MAC filtering (optional)
- [ ] Reviewed connected WiFi devices
- [ ] Checked system logs for anomalies
- [ ] Documented all configuration changes
- [ ] Saved configuration after changes
- [ ] Tested backup/restore procedure
- [ ] Set up regular security review schedule

## Incident Response

### Suspected Compromise

If you suspect device has been compromised:

1. **Immediate Actions:**
   ```bash
   # Check connected devices
   SU_WAP> display wifi associate
   SU_WAP> display dhcp server user all

   # Check active connections
   SU_WAP> netstat -na

   # Review logs
   SU_WAP> display syslog
   ```

2. **Change Passwords:**
   - Admin password
   - WiFi password
   - Review all passwords

3. **Review Configuration:**
   ```bash
   SU_WAP> display current-configuration
   SU_WAP> display firewall rule
   SU_WAP> display nat port mapping
   ```

4. **If Severely Compromised:**
   ```bash
   # Factory reset
   SU_WAP> restore manufactory
   ```

   Then reconfigure from scratch with secure settings.

5. **Contact ISP:**
   - Report potential security incident
   - Request log review
   - Ask about any suspicious TR-069 activity

### Prevention

Best defense is good security hygiene:
- Strong, unique passwords
- Regular monitoring
- Prompt updates
- Principle of least privilege
- Defense in depth
