# Living Room Remote

ESPHome configuration for a touch-screen remote control to manage Home Assistant scenes. Built for the Waveshare ESP32-C6-LCD-1.9 board with a 1.9" color display.

## Hardware

**Board**: Waveshare ESP32-C6-LCD-1.9
- ESP32-C6 microcontroller
- 1.9" ST7789V display (172x320 pixels)
- CST816 capacitive touch controller
- Built-in WiFi

## Features

**Touch Interface**
- Tab-based UI with "Fans" and "Lights" sections
- Three buttons per tab to trigger different scenes
- Auto-dimming backlight (dims to 5% after 60 seconds)
- Touch to wake display to full brightness

**Status Indicators**
- WiFi connection status with signal strength color coding
  - Green: Strong signal (> -60 dBm)
  - Yellow: Weak signal (-60 to -80 dBm)
  - Red: Poor signal or disconnected
- Home Assistant API connection status
  - Green: Connected
  - Red: Disconnected

**Scene Control**
- Fans: Off / Medium / High
- Lights: Off / Dim / Bright
- All actions trigger corresponding Home Assistant scenes

## Setup

### Prerequisites

1. ESPHome installed and configured
2. Home Assistant with ESPHome integration
3. Home Assistant scenes configured:
   - `scene.fans_off`
   - `scene.fans_medium`
   - `scene.fans_high`
   - `scene.fan_lights_off`
   - `scene.dim_fan_lights`
   - `scene.bright_fan_lights`

### Configuration

Create a `secrets.yaml` file with your credentials:

```yaml
wifi_ssid: "Your WiFi SSID"
wifi_password: "Your WiFi Password"
api_encryption_key: "your-32-character-encryption-key"
```

### Installation

1. Connect your ESP32-C6 board to your computer
2. Compile and upload the configuration:
   ```bash
   esphome run living-room-remote.yaml
   ```
3. Wait for the device to boot and connect to WiFi
4. Add to Home Assistant:
   - Settings → Devices & Services → Add Integration → ESPHome
   - Enter the API encryption key when prompted

## Display Layout

**Top Section (y: 0-48)**
- Tab bar with "Fans" and "Lights" tabs
- Active tab highlighted in green

**Content Section (y: 48-280)**
- Three buttons (72px height each, 8px spacing)
- Each button triggers a Home Assistant scene

**Status Bar (Bottom)**
- "WiFi" indicator (bottom-left)
- "HA" indicator (bottom-right)

## Customization

**Changing Scenes**
Edit the script sections to match your Home Assistant scene entity IDs:

```yaml
script:
  - id: scene_fans_off
    then:
      - homeassistant.service:
          service: scene.turn_on
          data:
            entity_id: scene.your_scene_name
```

**Adjusting Colors**
Colors are defined in hex format (0xRRGGBB):
- Active tab: `0x006446` (green)
- Button background: `0xFFFFFF` (white)
- Text color: `0x1E1E1E` (dark gray)

**Screen Timeout**
Change the dimming delay in the `wake_display` script:

```yaml
- delay: 60s  # Change to desired seconds
```

## Troubleshooting

**Display not responding to touch**
- Check ESPHome logs for touch coordinates
- Verify CST816D initialization messages on boot

**API not connecting**
- Verify encryption key matches in both ESPHome and Home Assistant
- Check that device shows as "Online" in ESPHome dashboard
- Remove and re-add the integration in Home Assistant

**Scenes not triggering**
- Verify scene entity IDs exist in Home Assistant
- Check Home Assistant logs for service call errors
- Ensure scenes are configured correctly

**Display appears inverted**
- Rotation is set to 180 degrees in configuration
- Adjust `rotation:` value if needed (0, 90, 180, 270)

## Technical Details

**Communication**
- I2C bus for touchscreen (SDA: GPIO 18, SCL: GPIO 8)
- SPI bus for display (CLK: GPIO 5, MOSI: GPIO 4)
- WiFi for Home Assistant API connection

**Display Configuration**
- Model: ST7789V
- Resolution: 172x320 (with 34px width offset)
- Color order: RGB
- Inverted colors enabled
- Manual update control (LVGL handles rendering)

**Power Management**
- Backlight PWM control on GPIO 15
- Default: Always on
- Auto-dim to 5% after timeout
