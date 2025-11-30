# Real-Time Chat App (Flutter + WebSocket + Firebase Authentication)

A real-time chat application built with **Flutter**, featuring **WebSocket-based messaging**, **Firebase Authentication**, and a clean, scalable architecture.

---

## 🚀 Features
- 🔐 **Firebase Authentication**
  - Email & Password login
  - Secure session handling
- 💬 **Real-Time Messaging (WebSocket)**
  - Join chat rooms
  - Live send & receive messages
  - JSON-based data protocol
- 👤 **1-to-1 Private Chat System**
- 📡 **WebSocket Node.js Server**
- 🧱 **Clean Architecture (Cubit / MVC)**
- 📱 Supports **Android / iOS / Web**

---

## 🛠️ Tech Stack

### Frontend (Flutter)
- Flutter 3.x
- Cubit / BLoC
- WebSocket Channel
- Firebase Authentication

### Backend (Node.js)
- WebSocket (`ws` package)

---

## 📂 Project Structure
```text
lib/
├── pages/
│ ├── login_page.dart
│ ├── chat_page.dart
│ └── rooms_page.dart
│
├── cubits/chat_cubit.dart
├── model/message_model.dart
├── services/
│ ├── websocket_services.dart
│ └── firebase_auth_service.dart
└── main.dart


```

## 🔧 WebSocket Server (Node.js)

Create `server.js`:

```javascript
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

const rooms = {};

wss.on('connection', (ws) => {
  ws.roomId = null;

  ws.on('message', (raw) => {
    try {
      const msg = JSON.parse(raw);

      if (msg.type === 'join') {
        ws.roomId = msg.room;
        rooms[msg.room] = rooms[msg.room] || [];
        rooms[msg.room].push(ws);
      }

      else if (msg.type === 'message') {
        const room = ws.roomId;
        if (!room) return;

        rooms[room].forEach(client => {
          if (client.readyState === WebSocket.OPEN) {
            client.send(JSON.stringify({
              type: 'message',
              from: msg.from,
              text: msg.text,
              ts: Date.now()
            }));
          }
        });
      }

    } catch (e) {
      console.error("Invalid message", e);
    }
  });

  ws.on('close', () => {
    if (ws.roomId && rooms[ws.roomId]) {
      rooms[ws.roomId] = rooms[ws.roomId].filter(c => c !== ws);
    }
  });
});

console.log("WebSocket server running on ws://localhost:8080");


Run:

node server.js

🌐 Flutter – Connect to WebSocket
final channel = WebSocketChannel.connect(
  Uri.parse('ws://192.168.1.106:8080'),
);

channel.sink.add(jsonEncode({
  'type': 'join',
  'room': 'room_123',
}));

channel.stream.listen((event) {
  final data = jsonDecode(event);
  print("New message: $data");
});

📨 Send a Message
channel.sink.add(jsonEncode({
  'type': 'message',
  'from': FirebaseAuth.instance.currentUser!.email,
  'text': messageController.text,
}));

🧩 Message Model
class MessageModel {
  final String from;
  final String text;
  final int ts;

  MessageModel.fromJson(Map<String, dynamic> json)
      : from = json['from'],
        text = json['text'],
        ts = json['ts'];
}

▶️ How to Run

Clone the project

Install dependencies:

flutter pub get


Run WebSocket server:

node server.js


Run Flutter app:

flutter run

📌 Future Improvements

📞 Add Voice & Video Calls (WebRTC)

💾 Store messages in Firebase Firestore

📸 Support sending images and files

🔔 Add push notifications (FCM)
