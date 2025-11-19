# Command Reference

Comprehensive reference for EG8145V5 CLI commands organized by category.

## Command Structure

Commands in the EG8145V5 CLI follow these patterns:
- `display <category> <details>` - Show information
- `set <category> <parameters>` - Configure settings
- `get <category>` - Retrieve configuration
- `clear <category>` - Clear statistics or reset counters

## System Information Commands

### Device Information

```bash
# Display comprehensive device information
display deviceInfo

# Display system information
display system info
display sysinfo

# Display software version
display version
display inner version

# Display serial number
display sn

# Display hardware information
display hardware info
```

### Resource Monitoring

```bash
# CPU information and usage
display cpu info

# Memory information
display memory info
display memory detail

# Process list
display process
display process detail

# Disk usage
display disk info
```

### System Logs

```bash
# Display system logs
display syslog

# Display detailed log information
display log info

# Display alarm information
display alarm current
display alarm history
```

## Network Configuration Commands

### Interface Management

```bash
# Display all interfaces
display if

# Display IP interface information
display ip interface

# Display interface statistics
display if statistics

# Display bridge information
display bridge
```

### WAN Configuration

```bash
# Display WAN information
display waninfo
display waninfo all
display waninfo all detail

# Display WAN connection status
display wan connection

# Display PPPoE information
display pppoe info
display pppoe status
```

### DHCP Commands

```bash
# DHCP Client
display dhcp client all
display dhcp client info

# DHCP Server
display dhcp server pool all
display dhcp server user all
display dhcp server statistics

# DHCP lease information
display dhcp server lease
```

### Routing

```bash
# Display routing table
display ip route

# Display dynamic routes
display dynamic route

# Display default gateway
display gateway

# Display ARP table
display arp
```

## WiFi Management Commands

### WiFi Status

```bash
# Display WiFi comprehensive information
display wifi information

# Display WiFi radio status
display wifi radio

# Display WiFi access point configuration
display wifi ap

# Display WiFi statistics
display wifi statistics
```

### WiFi Configuration

```bash
# Get basic WiFi settings
get wlan basic
get wlan enable
get wlan advance

# Set WiFi configuration
set wlan basic
set wlan enable
set ssid

# WiFi security
set wlan security
get wlan wps
```

### WiFi Clients

```bash
# Display connected WiFi clients
display wifi associate
get wlan associated

# Display WiFi client statistics
display wifi client statistics

# WiFi access control
display wifi mac filter
set wifi mac filter
```

### Advanced WiFi

```bash
# Channel and interference
display wifi neighbor
display wifi channel

# WiFi diagnostics
display wifi diagnose
```

## GPON/ONT Commands

### ONT Information

```bash
# Display ONT information
display onu info

# Display ONT registration status
display ont status

# Display optical transceiver information
display optic
```

### PON Statistics

```bash
# Display PON statistics
display pon statistics

# Display upstream statistics
display poncnt upstatistic

# Display downstream statistics
display poncnt dnstatistic

# Clear PON statistics
clear pon statistics
```

### Optical Monitoring

```bash
# Display optical power levels
display optic

# Expected RX power ranges:
# - Good: -8 to -28 dBm
# - Marginal: -28 to -30 dBm
# - Poor: below -30 dBm
```

## VoIP Commands

### VoIP Status

```bash
# Display VoIP information
display voip info

# Display VoIP service status
display voip service status

# Display SIP account status
vspa display user status
```

### Call Management

```bash
# Display call logs
display last call log

# Display active calls
display voip call status

# Call statistics
display voip statistics
```

### DSP Information

```bash
# Display DSP channel status
display dsp channel status
display dsp channel running status

# DSP diagnostics
display voip dsp para diagnose state
```

### VoIP Diagnostics

```bash
# RTP diagnostics
display voip rtpdiag

# Voice line test
set voicelinetest
```

## Security Commands

### Firewall

```bash
# Display firewall rules
display firewall rule

# Display firewall status
display firewall status

# Firewall statistics
display firewall statistics
```

### NAT Configuration

```bash
# Display NAT configuration
display nat

# Display port mapping (port forwarding)
display nat port mapping

# Display NAT sessions
display nat session
```

### MAC Filtering

```bash
# Display MAC filter rules
display lan mac filter

# Add MAC filter
lan mac filter add

# Enable/disable MAC filtering
lan mac filter enable
lan mac filter disable

# Remove MAC filter
lan mac filter delete
```

### Access Control

```bash
# Display access control lists
display acl

# Display IP filter rules
display ip filter
```

## Diagnostic Commands

### Network Diagnostics

```bash
# Ping test
ping <hostname_or_ip>
ping <hostname_or_ip> -c <count>

# Traceroute
traceroute <hostname_or_ip>

# DNS lookup
nslookup <hostname>

# Network statistics
netstat -na
netstat -r
```

### Port Statistics

```bash
# Display port statistics
display port statistics

# Display switch forwarding statistics
display sfwd port statistics

# Clear port statistics
clear port statistics
```

### System Diagnostics

```bash
# Run diagnostics
diagnose

# Display diagnostic information
display diagnose info

# Test specific components
set linetest
set voicelinetest
```

## Configuration Management

### Configuration Display

```bash
# Display current running configuration
display current-configuration

# Display startup configuration
display startup-configuration

# Display specific configuration sections
display config <section>
```

### Configuration Backup/Restore

```bash
# Save current configuration
save data

# Display backup list
display backup list

# Restore configuration
load configuration

# Factory reset
restore manufactory
```

### System Control

```bash
# Reboot device
reset

# Set timeout for CLI session
set timeout

# Logout
logout
quit
```

## Advanced Commands

### TR-069/CWMP

```bash
# Display TR-069 status
display cwmp status

# Display TR-069 information
display tr069 info

# TR-069 diagnostics
display cwmp diagnose
```

### OMCI Commands

```bash
# OMCI command interface (advanced)
omcicmd

# Display OMCI information
display omci info
```

### Service Management

```bash
# Enable/disable Telnet
set aptelnet

# Display service status
display service status

# Display running services
display process
```

### Shell Access

```bash
# Access Linux shell
shell

# Exit shell
exit
```

## Command Tips

1. **Tab Completion:**
   - Press Tab to auto-complete commands
   - Double Tab to see available options

2. **Command History:**
   - Use Up/Down arrows to navigate command history

3. **Help:**
   - Type `?` to see available commands
   - Add `?` after partial command for context help

4. **Output Control:**
   - Commands may have pagination
   - Press Space for next page
   - Press Q to quit output

5. **Saving Changes:**
   - Always run `save data` after configuration changes
   - Unsaved changes may be lost on reboot

## Common Command Workflows

### Check Device Health

```bash
SU_WAP> display deviceInfo
SU_WAP> display onu info
SU_WAP> display optic
SU_WAP> display cpu info
SU_WAP> display memory info
```

### Troubleshoot Connectivity

```bash
SU_WAP> display waninfo all
SU_WAP> display ip route
SU_WAP> ping 8.8.8.8
SU_WAP> traceroute 8.8.8.8
SU_WAP> display dhcp client all
```

### Monitor WiFi

```bash
SU_WAP> display wifi information
SU_WAP> display wifi associate
SU_WAP> display wifi neighbor
SU_WAP> display wifi statistics
```

### Check Fiber Quality

```bash
SU_WAP> display optic
SU_WAP> display pon statistics
SU_WAP> display onu info
```
