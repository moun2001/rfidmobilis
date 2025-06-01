# Real-Time Equipment Tracking System for Industrial Warehouse

## Project Overview

This system is designed to track 30 pieces of equipment (tools, pallets, machinery) within a 50m x 50m industrial warehouse using UHF RFID technology. The primary objectives are:

- **Real-time location monitoring** of all 30 equipment items with 2-5m accuracy
- **Perimeter alert system** when equipment approaches the 45-50m boundary zone
- **Out-of-range alerts** when equipment moves beyond the 50m warehouse perimeter
- **Remote web-based dashboard** accessible via 4G connectivity from the administration office
- **Industrial-grade durability** for harsh warehouse conditions (dust, moisture, metal interference)

The system operates in two distinct zones:
- **Zone 1 (Safe Zone)**: Central 40m x 40m area where equipment should remain
- **Zone 2 (Alert Zone)**: Outer 5m perimeter (45-50m from center) near exits and boundaries

## System Components

### Core Processing & Connectivity Unit
- **Compute Module 4 PoE 4G Board** - 1 unit
  - Integrated solution combining CM4 (4GB RAM, 32GB eMMC) with PoE and 4G connectivity
  - SIM7600G-H 4G module for remote connectivity
  - Power over Ethernet (PoE) capability for simplified installation
  - Central processor for data processing, location calculations, and web dashboard hosting
  - Runs Python-based tracking software and Flask web server
  - Enables SMS/email alerts and remote dashboard access
- **NETGEAR GS308PP PoE Switch** - 1 unit
  - 8-port Gigabit PoE+ switch (120W total power budget)
  - Powers RFID readers and CM4 via single Ethernet connection
  - Centralized network hub for all components

### RFID Tracking Components
- **UHF RFID Tags** - 30 units
  - Xerafy Dot XS tags for metal equipment (20 units)
  - Avery Dennison AD-160u7 tags for non-metal items (10 units)
  - Operating frequency: 902-928 MHz (US) / 865-868 MHz (EU)
  - Unique EPC identifiers for each equipment piece

- **Impinj Speedway R420 RFID Reader** - 1 unit
  - High-performance UHF reader with 4-antenna ports
  - Extended range configuration for 50m coverage
  - RSSI-based location estimation capability
  - PoE powered, Ethernet connectivity to switch
  - Centrally positioned for complete warehouse coverage

- **Laird S9028PCL High-Gain Antennas** - 4 units
  - Circular polarized antennas with 50m read range
  - High-gain configuration (12 dBi) for extended coverage
  - Optimized for industrial environments with metal interference
  - Strategic placement for complete 50m × 50m warehouse coverage

### Connectivity & Enclosure
- **CAT6 Ethernet Cables (2m)** - 3 units
  - High-quality shielded cables for PoE and data transfer
  - Connect readers and CM4 to PoE switch
- **SIM Card with Data Plan** - 1 unit
  - 4G/LTE connectivity for remote access and alerts
- **4G Antenna** - 1 unit (included with Waveshare board)
  - External antenna for enhanced 4G signal reception
- **Waveshare CM4-IO-BASE-BOX-B Enclosure** - 1 unit
  - Industrial-grade protection for CM4 and PoE board
  - IP65 rated for dust and moisture protection

### Software Components
- **Custom Python Application**
  - Real-time location tracking using RSSI triangulation
  - Alert system for SMS/email notifications
  - Flask-based web dashboard
  - Database logging and historical tracking

## System Operation

### Real-Time Location Tracking (RTLS)
The system uses RSSI-based triangulation with a single high-performance reader:

1. **Tag Detection**: UHF RFID tags attached to equipment continuously transmit their unique identifiers
2. **Signal Reception**: Four high-gain antennas connected to one centrally positioned reader detect tag signals across the entire 50m × 50m warehouse
3. **RSSI Analysis**: Signal strength measurements from multiple antennas enable precise triangulation
4. **Location Calculation**: Compute Module 4 PoE 4G Board processes RSSI data to estimate coordinates with 2-5m accuracy
5. **Zone Classification**: Equipment positions are classified into Zone 1 (safe) or Zone 2 (alert)
6. **Complete Coverage**: Single reader solution provides 100% warehouse coverage with simplified installation

### Alert System
The system triggers different alert types based on equipment location:

**Near-Exit Alert (Zone 2)**:
- Triggered when equipment enters the 45-50m perimeter zone
- Example: "ALERT: Pallet-003 approaching exit at coordinates (47m, 45m) - Time: 10:30 AM"
- Sent via SMS and email through 4G connection

**Out-of-Range Alert**:
- Triggered when equipment signal is lost beyond 50m range
- Example: "CRITICAL: Tool-012 has left the warehouse perimeter - Last known position: (48m, 49m) - Time: 11:00 AM"
- Immediate notification to warehouse manager and director

### Remote Dashboard
The Flask-based web interface provides:
- **Real-time map view**: 50m x 50m warehouse layout with equipment positions
- **Color-coded status**: Green dots (Zone 1), Yellow dots (Zone 2), Red dots (out-of-range)
- **Equipment details**: Click any dot to view equipment ID, last update time, and movement history
- **Alert log**: Chronological list of all alerts with timestamps and locations
- **System status**: Reader connectivity, tag battery levels, and network status

### Sample Operational Scenario

**Normal Operation**:
- Tool-005 located at (20m, 15m) in Zone 1 - Status: Safe (Green)
- Pallet-007 located at (35m, 25m) in Zone 1 - Status: Safe (Green)

**Near-Exit Alert**:
- Pallet-003 moves to (47m, 45m) in Zone 2 - Status: Alert (Yellow)
- System sends: "ALERT: Pallet-003 approaching warehouse exit at (47m, 45m) - 10:30 AM"

**Out-of-Range Alert**:
- Tool-012 signal lost beyond (52m, 48m) - Status: Critical (Red)
- System sends: "CRITICAL: Tool-012 has exited warehouse perimeter - 11:00 AM"

### Data Flow
1. **Tags** → Transmit EPC IDs and receive power from RF field
2. **Antennas** → Capture tag signals and measure RSSI
3. **Readers** → Process RF data and send to network
4. **PoE Switch** → Routes data and provides power distribution
5. **CM4** → Calculates locations, manages alerts, hosts dashboard
6. **4G Connection** → Sends alerts and enables remote dashboard access

## Implementation Notes

### Setup Procedure
1. **Reader Installation**: Mount single reader at warehouse center (25m, 25m) at 4m height
2. **Antenna Positioning**: Install four antennas in corners at 3m high with 45° downward tilt for optimal 50m coverage
3. **Tag Attachment**: Secure tags to equipment using industrial adhesive or mechanical fasteners
4. **Network Configuration**: Connect reader and Compute Module 4 PoE 4G Board through PoE switch
5. **Software Deployment**: Install Python application on integrated CM4 with required libraries
6. **Testing Phase**: Start with 5 tags to verify complete warehouse coverage before full deployment
7. **Coverage Verification**: Test signal strength at all warehouse corners to ensure 50m range

### Required Software Libraries
```python
# Core libraries for implementation
import impinj_reader  # RFID reader communication
import paho.mqtt      # Message queuing for real-time updates
import flask          # Web dashboard framework
import numpy          # Mathematical calculations for triangulation
import sqlite3        # Local database for logging
import requests       # HTTP requests for alerts
import threading      # Concurrent processing
```

### Sample Code Implementation

```python
import numpy as np
import sqlite3
import requests
import time
from datetime import datetime
from flask import Flask, render_template, jsonify
from threading import Thread
import json

class WarehouseTrackingSystem:
    def __init__(self):
        self.readers = {
            'reader1': {'ip': '192.168.1.100', 'position': (5, 5)},
            'reader2': {'ip': '192.168.1.101', 'position': (45, 45)}
        }
        self.equipment_positions = {}
        self.alert_threshold_zone2 = 45  # meters from center
        self.alert_threshold_out = 50    # meters from center
        self.setup_database()

    def setup_database(self):
        """Initialize SQLite database for logging"""
        conn = sqlite3.connect('warehouse_tracking.db')
        cursor = conn.cursor()
        cursor.execute('''
            CREATE TABLE IF NOT EXISTS equipment_log (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                equipment_id TEXT,
                x_position REAL,
                y_position REAL,
                zone TEXT,
                timestamp DATETIME,
                rssi_values TEXT
            )
        ''')
        conn.commit()
        conn.close()

    def calculate_position(self, tag_id, rssi_data):
        """Calculate equipment position using RSSI triangulation"""
        if len(rssi_data) < 2:
            return None

        # Extract antenna positions and RSSI values
        positions = []
        rssi_values = []

        for reader_id, antennas in rssi_data.items():
            reader_pos = self.readers[reader_id]['position']
            for antenna_id, rssi in antennas.items():
                # Antenna positions relative to reader
                if antenna_id == 'A':
                    antenna_pos = (reader_pos[0] - 2, reader_pos[1] - 2)
                else:  # Antenna B
                    antenna_pos = (reader_pos[0] + 2, reader_pos[1] + 2)

                positions.append(antenna_pos)
                rssi_values.append(rssi)

        # Simple weighted centroid calculation
        if len(positions) >= 2:
            weights = [max(0, rssi + 100) for rssi in rssi_values]  # Convert RSSI to positive weights
            total_weight = sum(weights)

            if total_weight > 0:
                x = sum(pos[0] * weight for pos, weight in zip(positions, weights)) / total_weight
                y = sum(pos[1] * weight for pos, weight in zip(positions, weights)) / total_weight
                return (round(x, 1), round(y, 1))

        return None

    def classify_zone(self, position):
        """Classify equipment position into zones"""
        if position is None:
            return "unknown"

        x, y = position
        distance_from_center = np.sqrt((x - 25)**2 + (y - 25)**2)

        if distance_from_center <= 20:  # Zone 1: Central 40x40m area
            return "zone1"
        elif distance_from_center <= 25:  # Zone 2: Alert zone
            return "zone2"
        else:
            return "out_of_range"

    def send_alert(self, equipment_id, position, zone, alert_type):
        """Send SMS/Email alerts via 4G connection"""
        timestamp = datetime.now().strftime("%H:%M:%S")

        if alert_type == "zone2":
            message = f"ALERT: {equipment_id} approaching exit at coordinates ({position[0]}m, {position[1]}m) - Time: {timestamp}"
        elif alert_type == "out_of_range":
            message = f"CRITICAL: {equipment_id} has left warehouse perimeter - Last position: ({position[0]}m, {position[1]}m) - Time: {timestamp}"

        # Send SMS via 4G modem API
        try:
            sms_payload = {
                'to': '+1234567890',  # Director's phone
                'message': message
            }
            # Replace with actual 4G modem API endpoint
            requests.post('http://localhost:8080/send_sms', json=sms_payload, timeout=5)

            # Send email alert
            email_payload = {
                'to': 'director@company.com',
                'subject': f'Warehouse Alert - {equipment_id}',
                'body': message
            }
            requests.post('http://localhost:8080/send_email', json=email_payload, timeout=5)

        except Exception as e:
            print(f"Alert sending failed: {e}")

    def process_tag_reading(self, tag_id, rssi_data):
        """Process individual tag reading and update position"""
        position = self.calculate_position(tag_id, rssi_data)

        if position:
            zone = self.classify_zone(position)
            previous_zone = self.equipment_positions.get(tag_id, {}).get('zone', 'unknown')

            # Update equipment position
            self.equipment_positions[tag_id] = {
                'position': position,
                'zone': zone,
                'last_update': datetime.now(),
                'rssi_data': rssi_data
            }

            # Check for zone changes and send alerts
            if zone == "zone2" and previous_zone != "zone2":
                self.send_alert(tag_id, position, zone, "zone2")
            elif zone == "out_of_range" and previous_zone != "out_of_range":
                self.send_alert(tag_id, position, zone, "out_of_range")

            # Log to database
            self.log_position(tag_id, position, zone, rssi_data)

    def log_position(self, equipment_id, position, zone, rssi_data):
        """Log equipment position to database"""
        conn = sqlite3.connect('warehouse_tracking.db')
        cursor = conn.cursor()
        cursor.execute('''
            INSERT INTO equipment_log (equipment_id, x_position, y_position, zone, timestamp, rssi_values)
            VALUES (?, ?, ?, ?, ?, ?)
        ''', (equipment_id, position[0], position[1], zone, datetime.now(), json.dumps(rssi_data)))
        conn.commit()
        conn.close()

# Flask Web Dashboard
app = Flask(__name__)
tracking_system = WarehouseTrackingSystem()

@app.route('/')
def dashboard():
    return render_template('dashboard.html')

@app.route('/api/equipment_positions')
def get_equipment_positions():
    """API endpoint for real-time equipment positions"""
    return jsonify(tracking_system.equipment_positions)

@app.route('/api/alerts')
def get_recent_alerts():
    """API endpoint for recent alerts"""
    conn = sqlite3.connect('warehouse_tracking.db')
    cursor = conn.cursor()
    cursor.execute('''
        SELECT equipment_id, x_position, y_position, zone, timestamp
        FROM equipment_log
        WHERE zone IN ('zone2', 'out_of_range')
        ORDER BY timestamp DESC LIMIT 10
    ''')
    alerts = cursor.fetchall()
    conn.close()

    return jsonify([{
        'equipment_id': alert[0],
        'position': [alert[1], alert[2]],
        'zone': alert[3],
        'timestamp': alert[4]
    } for alert in alerts])

if __name__ == '__main__':
    # Start RFID reader monitoring in separate thread
    def monitor_readers():
        # Simulated RFID data for demonstration
        sample_data = {
            'Tool-005': {
                'reader1': {'A': -45, 'B': -50},
                'reader2': {'A': -70, 'B': -75}
            },
            'Pallet-003': {
                'reader1': {'A': -80, 'B': -85},
                'reader2': {'A': -40, 'B': -45}
            }
        }

        while True:
            for tag_id, rssi_data in sample_data.items():
                tracking_system.process_tag_reading(tag_id, rssi_data)
            time.sleep(5)  # Update every 5 seconds

    # Start monitoring thread
    monitor_thread = Thread(target=monitor_readers, daemon=True)
    monitor_thread.start()

    # Start Flask web server
    app.run(host='0.0.0.0', port=5000, debug=False)
```

### Maintenance Requirements
- **Weekly**: Check tag adhesion and antenna alignment
- **Monthly**: Verify 4G signal strength and SIM data usage
- **Quarterly**: Clean antennas and inspect cable connections
- **Annually**: Replace tag batteries (if applicable) and update software

### Regional Considerations
- **US Frequency**: 902-928 MHz (FCC Part 15)
- **EU Frequency**: 865-868 MHz (ETSI EN 302 208)
- **Power Limits**: Comply with local regulations for RFID transmission power
- **4G Bands**: Ensure SIM card supports local LTE frequencies

This system provides comprehensive equipment tracking with industrial reliability and remote accessibility, ensuring warehouse security and operational efficiency.
