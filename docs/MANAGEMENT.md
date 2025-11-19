# Device Management and Access Control

Advanced device management features for the Huawei EchoLife EG8145V5, including access control, scheduling, and parental control options.

## Overview

The EG8145V5 provides basic device management capabilities through MAC filtering and firewall rules. However, it has limitations compared to modern consumer routers, particularly around time-based access control and granular content filtering.

## Table of Contents

- [MAC Address Filtering](#mac-address-filtering)
- [Time-Based Access Control](#time-based-access-control)
- [Content Filtering (Website Blocking)](#content-filtering-website-blocking)
- [Device Management Best Practices](#device-management-best-practices)
- [Workarounds and Alternatives](#workarounds-and-alternatives)

## MAC Address Filtering

### View Connected Devices

Before implementing MAC filtering, identify the devices on your network:

```bash
SU_WAP> display wifi associate
SU_WAP> display dhcp server user all
SU_WAP> display arp
```

Example output:
```
Hostname          SSID    Client Type    IP Address      MAC Address         Status
DESKTOP-ABC123    SSID5   MSFT 5.0       192.168.18.19   48:e7:da:e7:95:61   Online
```

### View Current MAC Filters

```bash
SU_WAP> display lan mac filter
```

### Enable MAC Filtering (Whitelist Mode)

Whitelist mode only allows specified MAC addresses to connect:

```bash
# Add allowed MAC addresses
SU_WAP> lan mac filter add
# Follow prompts:
# - Enter MAC address (e.g., aa:bb:cc:dd:ee:ff)
# - Select mode: Whitelist
# - Apply

# Repeat for each allowed device

# Enable MAC filtering
SU_WAP> lan mac filter enable

# Save configuration
SU_WAP> save data
```

### Block Specific MAC Address (Blacklist Mode)

Block specific devices while allowing all others:

```bash
# Add blocked MAC address
SU_WAP> lan mac filter add
# Follow prompts:
# - Enter MAC address to block
# - Select mode: Blacklist
# - Apply

# Enable MAC filtering
SU_WAP> lan mac filter enable

# Save configuration
SU_WAP> save data
```

### Remove MAC Filter

```bash
SU_WAP> lan mac filter delete
# Follow prompts to select which MAC to remove

SU_WAP> save data
```

### Disable MAC Filtering

```bash
SU_WAP> lan mac filter disable
SU_WAP> save data
```

### MAC Filtering Use Cases

**Pros:**
- Simple device blocking
- No per-device configuration required
- Works for both WiFi and wired connections
- Effective for blocking unknown devices

**Cons:**
- All-or-nothing (no time scheduling)
- MAC addresses can be spoofed
- Requires manual enable/disable for temporary blocks
- Not suitable for temporary restrictions

**Best For:**
- Permanently blocking unauthorized devices
- Creating a whitelist of approved devices
- Supplementing WiFi security

## Time-Based Access Control

### Native Limitations

**IMPORTANT:** The EG8145V5 does **not** support native time-based access control or scheduling features. Time-based restrictions require workarounds.

### Manual Scheduling (Option 1)

For occasional use, manually block/unblock devices:

```bash
# At restriction time (e.g., 20:30)
SU_WAP> lan mac filter add
# Enter MAC address: [device MAC]
# Mode: Blacklist

SU_WAP> lan mac filter enable
SU_WAP> save data

# When restriction ends (e.g., next morning)
SU_WAP> lan mac filter delete
# Select the MAC address to remove

SU_WAP> save data
```

**Pros:**
- No additional hardware required
- Simple to implement

**Cons:**
- Completely manual
- Easy to forget
- Not sustainable for daily use

### Automated Scheduling via External Script (Option 2)

Use an always-on device (Raspberry Pi, NAS, home server) to automate the process.

#### Prerequisites

- Always-on Linux device on the same network
- `sshpass` installed (`apt install sshpass` or `brew install sshpass`)
- SSH access to the EG8145V5

#### Create Block Script

```bash
#!/bin/bash
# File: block-device.sh

ROUTER_IP="192.168.18.1"
USERNAME="Eproot"  # Change if different ISP
ROUTER_PASS="your_router_password"
MAC_TO_BLOCK="48:e7:da:e7:95:61"  # Replace with actual MAC

sshpass -p "${ROUTER_PASS}" ssh -o StrictHostKeyChecking=no \
  -o HostKeyAlgorithms=+ssh-rsa \
  -o PubkeyAcceptedKeyTypes=+ssh-rsa \
  ${USERNAME}@${ROUTER_IP} << EOF
su
lan mac filter add ${MAC_TO_BLOCK}
lan mac filter enable
save data
quit
EOF
```

#### Create Unblock Script

```bash
#!/bin/bash
# File: unblock-device.sh

ROUTER_IP="192.168.18.1"
USERNAME="Eproot"
ROUTER_PASS="your_router_password"
MAC_TO_UNBLOCK="48:e7:da:e7:95:61"

sshpass -p "${ROUTER_PASS}" ssh -o StrictHostKeyChecking=no \
  -o HostKeyAlgorithms=+ssh-rsa \
  -o PubkeyAcceptedKeyTypes=+ssh-rsa \
  ${USERNAME}@${ROUTER_IP} << EOF
su
lan mac filter delete ${MAC_TO_UNBLOCK}
save data
quit
EOF
```

#### Make Scripts Executable

```bash
chmod +x block-device.sh
chmod +x unblock-device.sh
```

#### Schedule with Cron

```bash
# Edit crontab
crontab -e

# Add scheduling rules
# Block at 20:30 on weekdays (Monday-Friday)
30 20 * * 1-5 /path/to/block-device.sh

# Unblock at 06:00 on weekdays
0 6 * * 2-6 /path/to/unblock-device.sh

# Note: Day 2-6 is Tue-Sat to unblock after Mon-Fri blocks
```

#### Security Considerations

**WARNING:** Storing passwords in scripts is insecure. Better alternatives:

1. **Use SSH keys** (if router supports it - unlikely)
2. **Restrict script permissions:**
   ```bash
   chmod 700 block-device.sh unblock-device.sh
   chown root:root block-device.sh unblock-device.sh
   ```
3. **Use a dedicated password** just for automation
4. **Store in encrypted partition** or use a password manager

**Pros:**
- Fully automated
- Reliable scheduling
- Can handle complex schedules

**Cons:**
- Requires always-on device
- Password security concerns
- More complex setup
- Troubleshooting required if scripts fail

### Router-Level Scheduling (Option 3 - Recommended)

Deploy a secondary router with native parental controls:

#### Setup Options

**A. Bridge Mode + Secondary Router**

1. Configure EG8145V5 in bridge/passthrough mode (check with ISP)
2. Connect consumer router (TP-Link, ASUS, etc.) with parental controls
3. Configure time-based rules on the secondary router

**B. Dual Router Setup**

1. Keep EG8145V5 as primary gateway
2. Connect secondary router in AP mode
3. Use secondary router for WiFi with parental controls
4. Disable EG8145V5 WiFi

**Recommended Routers with Parental Controls:**
- **TP-Link Archer series** - Built-in parental controls with scheduling
- **ASUS RT-series** - AiProtection with device scheduling
- **Google Nest WiFi** - Family WiFi controls
- **UniFi Dream Machine** - Advanced scheduling and content filtering
- **Mikrotik routers** - Powerful but complex configuration

**Pros:**
- Native time-based scheduling
- Better parental control features
- More reliable than scripts
- No password storage issues
- Better WiFi performance (usually)

**Cons:**
- Additional hardware cost
- More complex network topology
- Potential double NAT issues (can be resolved)

## Content Filtering (Website Blocking)

### Native Limitations

The EG8145V5 has **very limited** content filtering capabilities:
- No URL filtering
- No domain-based blocking
- No keyword filtering
- Limited firewall rules

### IP-Based Blocking (Limited Solution)

Block specific IP addresses via firewall rules:

```bash
# View current firewall rules
SU_WAP> display firewall rule

# Display firewall configuration options
SU_WAP> display firewall status
```

**Limitations:**
- Requires knowing exact IP addresses
- Doesn't work with CDNs (multiple IPs)
- Doesn't work with dynamic IPs
- Websites change IPs frequently
- No domain name support

### DNS-Based Filtering (Partial Solution)

Configure network-wide DNS filtering (affects all devices):

#### Option A: Router-Level DNS

Set custom DNS servers on the EG8145V5:

```bash
SU_WAP> display dns
# Configure DNS to point to filtering service
```

**Filtering DNS Services:**
- **CleanBrowsing:** 185.228.168.168 (family filter)
- **OpenDNS Family Shield:** 208.67.222.123, 208.67.220.123
- **AdGuard DNS:** 94.140.14.15, 94.140.15.16

**Pros:**
- Network-wide filtering
- Blocks malware and adult content
- Easy to configure

**Cons:**
- Affects all devices
- Can't schedule per device
- Can be bypassed by changing device DNS
- May block legitimate sites

#### Option B: Pi-hole (Advanced)

Deploy Pi-hole for network-wide ad blocking and filtering:

1. Install Pi-hole on Raspberry Pi or similar device
2. Configure EG8145V5 to use Pi-hole as DNS server
3. Use Pi-hole groups to create per-device rules

**Pros:**
- Granular control
- Per-device filtering
- Scheduling capabilities
- Detailed logging and statistics

**Cons:**
- Requires additional hardware
- Complex setup
- Still can't do time-based per-device blocking natively
- Can be bypassed

## Device Management Best Practices

### Maintain Device Inventory

Keep a record of all network devices:

```bash
# Regular inventory check
SU_WAP> display wifi associate
SU_WAP> display dhcp server user all
```

**Recommended Documentation:**

| Hostname | MAC Address | IP Address | Device Type | Owner | Notes |
|----------|-------------|------------|-------------|-------|-------|
| DESKTOP-PC | aa:bb:cc:dd:ee:ff | 192.168.18.10 | Windows PC | John | Main computer |
| iPhone-Jane | 11:22:33:44:55:66 | 192.168.18.15 | iPhone 13 | Jane | Personal phone |

### Assign Static IPs

For devices you want to manage, assign static DHCP leases:

```bash
SU_WAP> display dhcp server pool all
# Configure static DHCP assignments via web interface (easier)
```

**Benefits:**
- Consistent IP addressing
- Easier firewall rule management
- Better logging and monitoring
- Simplifies troubleshooting

### Regular Monitoring

**Weekly Checks:**
```bash
# Check connected devices
SU_WAP> display wifi associate

# Review DHCP leases
SU_WAP> display dhcp server user all

# Check for unknown MACs
SU_WAP> display arp
```

**Monthly Audits:**
- Review all MAC filters
- Update device inventory
- Check for unauthorized devices
- Verify firewall rules

### Configuration Backup

Always backup configuration after changes:

```bash
SU_WAP> save data
SU_WAP> display backup list
```

Keep offline records of:
- MAC filter rules
- Device inventory
- Network topology
- Configuration changes log

## Workarounds and Alternatives

### Client-Side Solutions

For Windows, macOS, and Linux devices:

#### Windows Family Safety (Windows 10/11)

1. Create Microsoft Family account
2. Add child accounts
3. Configure screen time limits via account.microsoft.com/family
4. Enforces time limits even if network allows access

**Pros:**
- Built into Windows
- Cloud-managed
- Cross-device sync
- Cannot be easily bypassed

**Cons:**
- Requires Microsoft account
- Only works on Windows
- User can create local account to bypass

#### Third-Party Software

**For Windows:**
- **Cold Turkey Blocker** - Schedule website and application blocking
- **FocusMe** - Time-based internet restrictions
- **Qustodio** - Comprehensive parental control

**For macOS:**
- **Screen Time** (built-in) - Native scheduling
- **Freedom** - Cross-platform blocking
- **Focus** - Scheduled restrictions

**For Mobile Devices:**
- **Google Family Link** - Android parental controls
- **Apple Screen Time** - iOS native restrictions
- **Bark** - Content monitoring and filtering

**Pros:**
- Granular control
- Hard to bypass
- Better content filtering
- Detailed reporting

**Cons:**
- Per-device installation required
- Usually paid subscriptions
- Can be uninstalled (admin controls help)

### Network Architecture Solutions

#### VLAN Segmentation

If you have managed switches and advanced networking:

1. Create separate VLANs for different device types
2. Route restricted VLAN through filtering gateway
3. Apply different firewall rules per VLAN

**Requires:**
- Managed switch with VLAN support
- Advanced networking knowledge
- Router/firewall with VLAN support

#### Guest Network Isolation

Use separate SSIDs for different access levels:

1. Main network: Full access
2. Guest network: Restricted access
3. Kids network: Filtered/scheduled access

**On EG8145V5:**
```bash
SU_WAP> get wlan basic
# Check if multiple SSIDs supported (varies by firmware)
```

**Note:** EG8145V5 guest network support varies by ISP configuration.

### Replacement Router Solutions

For comprehensive parental controls, consider replacing or supplementing the EG8145V5:

#### Option 1: OpenWRT/DD-WRT Compatible Router

Flash OpenWRT on compatible hardware for advanced features:

**Features:**
- Time-based firewall rules
- MAC-based scheduling
- URL filtering
- Bandwidth management
- Custom scripts

**Popular Compatible Routers:**
- TP-Link Archer C7
- Linksys WRT series
- Netgear Nighthawk (select models)

#### Option 2: Enterprise Solutions

For maximum control:

- **UniFi Dream Machine** - Professional-grade controls
- **Mikrotik routers** - Powerful but complex
- **pfSense/OPNsense** - Open-source firewall/router
- **Firewalla** - Purpose-built parental control device

## Troubleshooting

### MAC Filter Not Working

**Problem:** Device still connects despite MAC filter

**Solutions:**

1. **Verify filter is enabled:**
   ```bash
   SU_WAP> display lan mac filter
   ```

2. **Check MAC address is correct:**
   - MAC addresses are case-insensitive
   - Use colon format: `aa:bb:cc:dd:ee:ff`
   - Some devices have randomized MACs

3. **Device using MAC randomization:**
   - Modern iOS/Android randomize MAC addresses
   - Disable "Private WiFi Address" (iOS) or "Randomized MAC" (Android)

4. **Check filter mode:**
   - Blacklist: Blocks listed MACs
   - Whitelist: Only allows listed MACs

5. **Restart WiFi:**
   - Disconnect device from WiFi
   - Wait 30 seconds
   - Attempt reconnection

### Script Automation Failures

**Problem:** Cron scripts not executing

**Debugging Steps:**

1. **Test script manually:**
   ```bash
   /path/to/block-device.sh
   ```

2. **Check script permissions:**
   ```bash
   ls -l /path/to/block-device.sh
   chmod +x /path/to/block-device.sh
   ```

3. **Verify cron is running:**
   ```bash
   systemctl status cron  # or crond
   ```

4. **Check cron logs:**
   ```bash
   grep CRON /var/log/syslog
   tail -f /var/log/cron
   ```

5. **Add logging to script:**
   ```bash
   #!/bin/bash
   echo "$(date): Script started" >> /var/log/router-control.log
   # ... rest of script
   echo "$(date): Script completed" >> /var/log/router-control.log
   ```

6. **SSH connection issues:**
   - Verify router IP is reachable
   - Test SSH connection manually
   - Check sshpass is installed
   - Verify password is correct

### Device Bypassing Restrictions

**Problem:** User bypasses MAC filter or time restrictions

**Common Bypass Methods:**

1. **MAC Address Spoofing:**
   - User clones allowed MAC address
   - **Solution:** Combine with IP monitoring, VLAN isolation

2. **Alternative Network:**
   - Using mobile hotspot
   - **Solution:** Client-side controls, physical device management

3. **Factory Reset Router:**
   - Advanced users may reset configuration
   - **Solution:** Physical access control, tamper-evident seals

4. **Changing Device DNS:**
   - Bypassing DNS filtering
   - **Solution:** Block port 53 to external servers, force router DNS

5. **VPN Usage:**
   - Bypassing content filters
   - **Solution:** Block VPN ports (may affect legitimate use)

**Defense in Depth:**
- Combine multiple methods (MAC + DNS + client software)
- Regular monitoring and audits
- Open communication about restrictions
- Physical device controls for younger children

## Summary and Recommendations

### EG8145V5 Capabilities

**What It Can Do:**
- ✅ Block devices by MAC address (all-or-nothing)
- ✅ Basic firewall rules
- ✅ View connected devices
- ✅ DHCP management

**What It Cannot Do:**
- ❌ Time-based access scheduling
- ❌ Per-device time restrictions
- ❌ URL/domain filtering
- ❌ Content category blocking
- ❌ Application-level filtering
- ❌ Bandwidth scheduling

### Recommended Approaches

**For Basic Device Blocking:**
- Use native MAC filtering
- Manual enable/disable as needed

**For Time-Based Restrictions (Weekday Evening Example):**
- **Best:** Add secondary router with parental controls
- **Good:** Automated scripts with cron (requires always-on device)
- **Acceptable:** Manual MAC filtering (daily effort required)
- **Alternative:** Client-side software controls

**For Content Filtering:**
- **Best:** Secondary router with content filtering
- **Good:** Pi-hole with custom blocklists
- **Acceptable:** DNS-based filtering (network-wide)
- **Alternative:** Client-side filtering software

**For Comprehensive Control:**
- Deploy dedicated parental control router
- Consider UniFi, ASUS, or TP-Link with robust features
- Use in conjunction with client-side controls
- Maintain open communication with users

### Device Limitations

The EG8145V5 is ISP-managed equipment designed for basic GPON ONT functionality. It is **not designed** for advanced parental controls or device management. For these features, supplementary hardware or software solutions are necessary.
