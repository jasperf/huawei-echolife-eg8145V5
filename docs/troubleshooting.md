# Troubleshooting Guide

Common issues and their solutions for the Huawei EchoLife EG8145V5.

## No Internet Connection

### Symptom
No internet connectivity on connected devices, or WAN connection shows as down.

### Diagnostic Steps

1. **Check Physical Fiber Connection**
   ```bash
   SU_WAP> display optic
   ```

   Look for:
   - **RX Power:** Should be between -8 and -28 dBm
   - **TX Power:** Typically 0 to +5 dBm
   - **Temperature:** Should be within normal range

   | RX Power Level | Status | Action |
   |----------------|--------|--------|
   | -8 to -28 dBm | Good | No action needed |
   | -28 to -30 dBm | Marginal | Monitor, may need ISP check |
   | Below -30 dBm | Poor | Contact ISP - fiber issue |
   | No signal | Critical | Check fiber cable, contact ISP |

2. **Check ONT Registration**
   ```bash
   SU_WAP> display onu info
   ```

   Verify:
   - ONT State: Should show "Registered" or "Working"
   - OLT Connection: Should show connection to OLT

   If not registered:
   - Wait 5-10 minutes for auto-registration
   - Check with ISP about activation status
   - Verify serial number is correct in ISP system

3. **Check WAN Configuration**
   ```bash
   SU_WAP> display waninfo all detail
   ```

   Look for:
   - Connection Status: Should be "Connected"
   - IP Address: Should have valid IP (not 0.0.0.0)
   - DNS Servers: Should be configured

   If WAN is down:
   ```bash
   SU_WAP> display pppoe status  # If using PPPoE
   SU_WAP> display dhcp client all  # If using DHCP
   ```

4. **Test Basic Connectivity**
   ```bash
   # Test local gateway
   SU_WAP> ping 192.168.18.1

   # Test ISP gateway (check your gateway IP first)
   SU_WAP> display ip route
   SU_WAP> ping <gateway_ip>

   # Test external DNS
   SU_WAP> ping 8.8.8.8

   # Test DNS resolution
   SU_WAP> nslookup google.com
   ```

5. **Check Routing**
   ```bash
   SU_WAP> display ip route
   ```

   Verify default route (0.0.0.0) is present and pointing to correct gateway.

### Common Causes and Solutions

| Cause | Symptoms | Solution |
|-------|----------|----------|
| Fiber disconnected | No RX power | Check fiber cable connections |
| ISP outage | Good optics, no WAN IP | Contact ISP |
| Authentication failure | PPPoE error | Verify credentials with ISP |
| DHCP failure | No IP assigned | Check DHCP client status |
| DNS issues | Can ping IPs but not domains | Check DNS configuration |

## WiFi Issues

### WiFi Not Visible

1. **Check WiFi Radio Status**
   ```bash
   SU_WAP> display wifi radio
   ```

   Verify:
   - Radio is enabled for both 2.4GHz and 5GHz
   - Channel is assigned
   - Power output is normal

2. **Enable WiFi if Disabled**
   ```bash
   SU_WAP> set wlan enable
   SU_WAP> save data
   ```

3. **Check SSID Configuration**
   ```bash
   SU_WAP> get wlan basic
   ```

   Verify:
   - SSID is configured
   - SSID broadcast is enabled (not hidden)
   - Correct frequency band

4. **Check for Interference**
   ```bash
   SU_WAP> display wifi neighbor
   ```

   Look for:
   - Overlapping channels
   - Strong neighboring networks

   Consider changing channel if interference is detected.

### Cannot Connect to WiFi

1. **Verify Password**
   ```bash
   SU_WAP> get wlan basic
   ```

   Check encryption type and ensure clients use correct password.

2. **Check WiFi Security Settings**
   ```bash
   SU_WAP> get wlan advance
   ```

   Ensure:
   - WPA2 or WPA3 is enabled
   - Compatible with client devices
   - MAC filtering isn't blocking device

3. **Check MAC Filtering**
   ```bash
   SU_WAP> display wifi mac filter
   ```

   If enabled, ensure client MAC is allowed.

4. **Check Connected Client Limit**
   ```bash
   SU_WAP> display wifi associate
   ```

   Device may have maximum client limit reached.

### Poor WiFi Performance

1. **Check Signal Strength**
   - Move closer to device
   - Check for physical obstructions
   - Consider WiFi extender

2. **Check for Interference**
   ```bash
   SU_WAP> display wifi neighbor
   SU_WAP> display wifi channel
   ```

   Change to less congested channel if needed.

3. **Monitor WiFi Statistics**
   ```bash
   SU_WAP> display wifi statistics
   SU_WAP> display wifi client statistics
   ```

   Look for:
   - High error rates
   - Low throughput
   - Retransmissions

4. **Optimize WiFi Settings**
   - Use 5GHz for better performance (shorter range)
   - Use 2.4GHz for better coverage (lower speed)
   - Disable legacy 802.11b/g if all devices support newer standards

## VoIP Not Working

### No Dial Tone

1. **Check VoIP Service Status**
   ```bash
   SU_WAP> display voip info
   ```

   Verify:
   - VoIP service is enabled
   - Registration status is "Registered"

2. **Check SIP Registration**
   ```bash
   SU_WAP> vspa display user status
   ```

   Should show registered status with SIP server.

3. **Check Physical Connection**
   - Verify phone is properly connected to POTS port
   - Try different phone port
   - Test with different phone handset

4. **Run Line Test**
   ```bash
   SU_WAP> set voicelinetest
   ```

### Cannot Make/Receive Calls

1. **Check VoIP Configuration**
   ```bash
   SU_WAP> display voip info
   ```

2. **Check DSP Status**
   ```bash
   SU_WAP> display dsp channel status
   SU_WAP> display dsp channel running status
   ```

3. **Check Network Quality**
   ```bash
   SU_WAP> display voip rtpdiag
   ```

   Look for:
   - Packet loss
   - Jitter
   - Latency issues

4. **Check Call Logs**
   ```bash
   SU_WAP> display last call log
   ```

### Poor Call Quality

1. **Check RTP Diagnostics**
   ```bash
   SU_WAP> display voip rtpdiag
   ```

2. **Verify Network Stability**
   - High latency or packet loss affects VoIP quality
   - Test internet connection quality
   - Consider QoS settings

3. **Check for Network Congestion**
   ```bash
   SU_WAP> display port statistics
   ```

## Performance Issues

### Slow Internet Speed

1. **Check Optical Signal Quality**
   ```bash
   SU_WAP> display optic
   ```

   Poor optical signal can cause speed issues.

2. **Monitor PON Statistics**
   ```bash
   SU_WAP> display pon statistics
   ```

   Look for:
   - Error packets
   - Discarded packets
   - CRC errors

3. **Check for Bandwidth Saturation**
   ```bash
   SU_WAP> display port statistics
   ```

4. **Test Speed**
   - Use speed test from device connected to LAN port
   - Compare to WiFi speeds
   - Test at different times of day

### High Latency

1. **Check Local Network**
   ```bash
   # Ping local gateway (should be <1ms)
   SU_WAP> ping 192.168.18.1

   # Ping ISP gateway
   SU_WAP> ping <isp_gateway>

   # Traceroute to diagnose where latency occurs
   SU_WAP> traceroute 8.8.8.8
   ```

2. **Monitor System Resources**
   ```bash
   SU_WAP> display cpu info
   SU_WAP> display memory info
   ```

   High CPU or memory usage can cause latency.

### Device Unresponsive

1. **Check System Resources**
   ```bash
   SU_WAP> display cpu info
   SU_WAP> display memory info
   SU_WAP> display process
   ```

2. **Check System Logs**
   ```bash
   SU_WAP> display syslog
   SU_WAP> display log info
   ```

3. **Restart Services** (if specific service is problematic)
   - Contact ISP for service-specific issues
   - May require reboot

4. **Reboot Device**
   ```bash
   SU_WAP> reset
   ```

   Or use physical power cycle if device is completely unresponsive.

## GPON/Fiber Issues

### Fiber Signal Issues

1. **Check Optical Levels**
   ```bash
   SU_WAP> display optic
   ```

2. **Common Fiber Issues:**

   | Issue | Symptoms | Solution |
   |-------|----------|----------|
   | Bent fiber | Low RX power, fluctuating signal | Straighten fiber cable |
   | Dirty connector | Low RX power, intermittent | Clean SC/APC connector |
   | Damaged fiber | No signal or very low RX | Replace fiber cable |
   | Loose connection | Fluctuating signal | Secure fiber connections |

3. **Monitor PON Errors**
   ```bash
   SU_WAP> display pon statistics
   ```

   Clear statistics and monitor:
   ```bash
   SU_WAP> clear pon statistics
   # Wait a few minutes
   SU_WAP> display pon statistics
   ```

### ONT Registration Issues

1. **Check Registration Status**
   ```bash
   SU_WAP> display onu info
   ```

2. **Common Registration Issues:**
   - Serial number mismatch with ISP
   - VLAN configuration incorrect
   - OLT not configured for this ONT
   - ISP provisioning incomplete

3. **Contact ISP if:**
   - ONT shows good optical levels but won't register
   - Registration was working but stopped
   - After ISP network maintenance

## Configuration Issues

### Cannot Save Configuration

1. **Check Flash Memory**
   ```bash
   SU_WAP> display disk info
   ```

2. **Retry Save**
   ```bash
   SU_WAP> save data
   ```

3. **If persistent, may need factory reset:**
   ```bash
   SU_WAP> restore manufactory
   ```

   **Warning:** This erases all configuration!

### Forgot Password

1. **ISP Reset:**
   - Contact ISP for password reset
   - ISP can reset via TR-069 remotely

2. **Factory Reset (Last Resort):**
   - Hardware reset: Hold reset button 10+ seconds
   - Or via CLI: `restore manufactory`
   - **Note:** Erases all configuration including ISP settings

### Lost Configuration After Reboot

1. **Ensure Configuration is Saved:**
   ```bash
   SU_WAP> save data
   ```

   Always save after making changes!

2. **Check for Auto-Provisioning:**
   - ISP may use TR-069 to auto-provision
   - TR-069 may override manual settings
   - Contact ISP if automatic provisioning is overriding your settings

## Hardware Issues

### Device Won't Power On

1. Check power adapter
2. Try different power outlet
3. Check for physical damage
4. Contact ISP for replacement

### Ethernet Port Not Working

1. **Check Port Status:**
   ```bash
   SU_WAP> display if
   SU_WAP> display port statistics
   ```

2. **Try:**
   - Different Ethernet cable
   - Different port
   - Different client device

3. **Check for:**
   - Physical damage to port
   - Port disabled in configuration

### LED Indicators

Refer to device manual for LED meanings, but typically:
- **Power:** Solid green = normal
- **PON:** Solid green = registered, flashing = connecting
- **LAN:** Green when active, flashing during data transfer
- **WLAN:** Green when WiFi enabled

## Getting Additional Help

### Information to Collect Before Contacting ISP

```bash
# Gather diagnostic information
SU_WAP> display deviceInfo
SU_WAP> display onu info
SU_WAP> display optic
SU_WAP> display waninfo all detail
SU_WAP> display syslog
```

### Log Collection

Save output from diagnostic commands to provide to ISP or for troubleshooting.

### Factory Reset (Last Resort)

**Warning:** This will erase ALL configuration including ISP settings!

```bash
SU_WAP> restore manufactory
```

Or hardware reset:
1. Locate reset button (usually small hole)
2. Use paperclip to press and hold button
3. Hold for 10+ seconds
4. Device will reboot with factory settings

After factory reset, you'll need ISP to reconfigure TR-069 settings.
