# Real-time Parking Availability App

This project comprises two components: a Python server running on a Raspberry Pi and a Dart-based Flutter app. The server simulates real-time parking availability data and transmits it to the Flutter app via a WebSocket connection.

## Python Server (Raspberry Pi)

### parking_server.py

```python
# Python code for the Raspberry Pi server
import asyncio
import websockets

async def parking_data(websocket, path):
    while True:
        # Simulate real-time parking availability data (replace with actual data source).
        data = "Parking spaces available: 10"
        await websocket.send(data)
        await asyncio.sleep(5)  # Update data every 5 seconds.

start_server = websockets.serve(parking_data, "0.0.0.0", 8765)

asyncio.get_event_loop().run_until_complete(start_server)
asyncio.get_event_loop().run_forever()
```

- This section of the code establishes a Python server for the Raspberry Pi. The server leverages the websockets library to manage WebSocket connections. It continuously sends the message "Parking spaces available: 10" to connected clients and updates the data every 5 seconds.

## Flutter App

### parking_app.dart

```dart
import 'package:flutter/material.dart';
import 'package:web_socket_channel/io.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: ParkingAvailabilityScreen(),
    );
  }
}

class ParkingAvailabilityScreen extends StatefulWidget {
  @override
  _ParkingAvailabilityScreenState createState() => _ParkingAvailabilityScreenState();
}

class _ParkingAvailabilityScreenState extends State<ParkingAvailabilityScreen> {
  final channel = IOWebSocketChannel.connect('ws://your_raspberry_pi_ip:8765');
  String parkingData = 'Waiting for data...';

  @override
  void initState() {
    super.initState();
    channel.stream.listen((data) {
      setState(() {
        parkingData = data;
      });
    });
  }

  @override
  void dispose() {
    channel.sink.close();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Parking Availability App'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'Parking Availability:',
              style: TextStyle(fontSize: 24),
            ),
            Text(
              parkingData,
              style: TextStyle(fontSize: 36, fontWeight: FontWeight.bold),
            ),
          ],
        ),
      ),
    );
  }
}
```

- This section of the code defines the Flutter app. The app initializes with a "Waiting for data..." message and updates the parking availability information in real-time as received from the Python server through a WebSocket connection.

This project combines a Raspberry Pi server and a Flutter app to provide real-time parking availability information to users.