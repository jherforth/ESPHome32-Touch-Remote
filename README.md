# ESPHome Living Room Remote

ESPHome configuration for Waveshare ESP32-C6-LCD-1.9 board to control Home Assistant devices via touch interface.

## Critical Fix Applied

### Problem
- WiFi showed connected but no API connection to Home Assistant
- Touch commands not working
- State updates not syncing from Home Assistant

### Root Cause
1. **Service call syntax errors**: Mixed `variables` and `data_template` which doesn't work in ESPHome
2. **Touch coordinates misaligned**: Zones didn't match the 58-pixel row heights
3. **Missing API encryption**: Device wasn't properly authenticated with Home Assistant

### Solution
All three issues have been fixed in this update.

## CRITICAL: Re-Adding Device to Home Assistant

**If you already added your device to Home Assistant before, you MUST remove and re-add it:**

1. **Remove old device**:
   - Go to Settings → Devices & Services → ESPHome
   - Find your device "Living Room Remote"
   - Click the device and choose "Delete"

2. **Re-flash with updated config**:
   - Upload the updated YAML to your ESP32-C6 board
   - Wait for it to boot and connect to WiFi

3. **Re-add to Home Assistant**:
   - Go to Settings → Devices & Services
   - Click "Add Integration"
   - Select "ESPHome"
   - It should auto-discover your device
   - Enter the API encryption key from your secrets.yaml

4. **Verify connection**:
   - Look at the bottom of your touch remote display
   - You should see "WiFi" in green AND "Connected" in green
   - If "Disconnected" shows, the API encryption key doesn't match

## What's on the Display

**Bottom Status Bar:**
- "WiFi" (green) = WiFi connected
- "Connected" (green) = Home Assistant API connected
- "Disconnected" (yellow) = API not connected - re-add device

**Device Rows (touch to cycle Off → Low → Med → High):**
- Row 1: Fan 1 (y: 0-58)
- Row 2: Fan 2 (y: 58-116)
- Row 3: Light 1 (y: 116-174)
- Row 4: Light 2 (y: 174-232)
- Row 5: Light 3 (y: 232-290)

## Debugging

To check logs from your device:
1. Open ESPHome dashboard
2. Click "Logs" on your device
3. Watch for:
   - `[touch]` logs showing touch coordinates when you tap
   - `[ha_sensor]` logs showing when Home Assistant updates are received
   - `[api]` logs showing connection status

Common issues:
- **"Disconnected" shows**: API encryption key mismatch - remove and re-add device
- **Touch not registering**: Check logs for touch coordinates, may need transform adjustment
- **No state updates**: Verify entity IDs match your actual devices in Home Assistant
