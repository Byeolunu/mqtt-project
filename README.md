# Smart Home MQTT Dashboard

A Node-RED based smart home automation system with interactive MQTT-controlled visualization of a multi-room floor plan.

## Project Overview

This project provides a complete smart home automation setup with:
- **Interactive floor plan visualization** with multiple rooms (living room, bedroom, kitchen, bathroom, office)
- **MQTT-based communication** for real-time device control and status updates
- **Live status indicators** showing device states (lights, TV, AC, heating, door, fridge temperature)
- **Web-based UI dashboard** for monitoring and controlling home devices

## Architecture & Flow

### 1. **MQTT Input → SVG Update Pipeline**
```
MQTT Broker → mqtt_in (subscribe to home/#) → svg_update (process messages) → DOM updates
```

The system listens to MQTT messages on the `home/#` topic and processes them in real-time.

### 2. **Message Topics & Handlers**

| Topic | Payload | Action |
|-------|---------|--------|
| `home/living/light` | ON/OFF | Toggles living room light overlay on SVG |
| `home/tv` | ON/OFF | Shows/hides TV status indicator |
| `home/ac/living` | ON/OFF | Displays AC status badge |
| `home/ac/bedroom` | ON/OFF | Displays AC status badge |
| `home/heat/living` | ON/OFF | Displays heating status badge |
| `home/heat/bedroom` | ON/OFF | Displays heating status badge |
| `home/fridge/temp` | Temperature (°C) | Updates fridge temperature display |
| `home/door` | OPEN/CLOSED | Shows door status indicator |

### 3. **UI Components**

#### **Floor Plan Visualization (ui_template)**
- SVG-based interactive floor plan showing 5 rooms
- Color-coded areas: blue (living), purple (bedroom), tan (kitchen), pink (bathroom), green (office)
- Includes furniture layout (bed, sofa, kitchen appliances, etc.)
- Dynamic element overlays for status indicators

#### **Control Switches (ui_switch)**
- **TV Switch**: Publishes to `home/tv` (ON/OFF)
- Additional switches for lights, AC, heating controls

#### **Status Indicators**
- **Light overlays**: Yellow transparent overlay when ON
- **TV playing**: Blue display animation when active
- **AC badges**: Green indicators when cooling
- **Heat badges**: Red indicators when heating
- **Door indicator**: Visual representation of door lock state
- **Fridge temperature**: Real-time temperature display with warning when > 8°C

### 4. **Data Flow Example**

**Scenario: User toggles TV switch**
1. User clicks "TV ON/OFF" switch in dashboard
2. ui_switch sends MQTT message: `home/tv: "ON"`
3. mqtt_in node receives the message
4. svg_update script captures message
5. JavaScript finds `#tv-playing` SVG element
6. Element opacity changes from 0 to 1
7. TV indicator appears on floor plan visualization

### 5. **Message Processing Logic**

The `svg_update` template contains JavaScript that:
- Watches incoming MQTT messages via `scope.$watch('msg')`
- Matches message topic to determine which DOM element to update
- Updates SVG element properties (opacity, fill, text content)
- Handles special cases like fridge temperature thresholds

### 6. **Room Layout**

```
┌─────────────────────────┬──────────────────────────┐
│                         │                          │
│   LIVING ROOM          │      BEDROOM             │
│   (lights, TV, sofa)   │   (bed, wardrobe, AC)    │
│                         │                          │
├─────────────────────────┼──────────────────────────┤
│   KITCHEN              │   BATHROOM      │  OFFICE │
│   (stove, fridge,      │   (tub, sink)   │ (desk,  │
│    dining table)       │                 │ shelves)│
└─────────────────────────┴──────────────────────────┘
```

## Getting Started

### Prerequisites
- Node-RED installed and running
- MQTT broker (e.g., Mosquitto) configured and accessible
- Web browser for dashboard access

### Setup Instructions

1. **Import the flow** into Node-RED
2. **Configure MQTT broker** connection (update broker details if needed)
3. **Deploy the flow**
4. **Access the dashboard** via Node-RED UI
5. **Publish test messages** using MQTT client tools:
   ```bash
   mosquitto_pub -t home/tv -m "ON"
   mosquitto_pub -t home/living/light -m "ON"
   mosquitto_pub -t home/fridge/temp -m "4"
   ```

## Message Examples

### Turn on living room light:
```json
Topic: home/living/light
Payload: ON
```

### Update bedroom temperature control:
```json
Topic: home/heat/bedroom
Payload: ON
```

### Update fridge temperature:
```json
Topic: home/fridge/temp
Payload: 5
```

## Debugging Tips

- **Enable debug nodes** to monitor all incoming MQTT messages
- **Check browser console** (F12) for JavaScript errors
- **Verify MQTT connection** settings in mqtt_in node properties
- **Test with mosquitto_pub/sub** tools to verify message flow
- **Use Node-RED debug sidebar** to inspect message payloads

## Future Enhancements

- Add thermostat control with temperature sliders
- Implement motion/door sensors for automation triggers
- Add scheduling capabilities (cron-based automations)
- Create statistics dashboard for energy consumption
- Add voice control integration (Alexa/Google Home)
- Implement authentication for dashboard access
- Add mobile app integration

## File Structure

```
mqtt/
├── mqtt_project.json     # Main Node-RED flow configuration
└── README.md             # This file
```

## License

[Your License Here]

## Support

For issues or questions, refer to:
- [Node-RED Documentation](https://nodered.org/docs/)
- [MQTT Protocol Reference](http://mqtt.org/)
- [Node-RED Dashboard Documentation](https://github.com/node-red/node-red-dashboard)
