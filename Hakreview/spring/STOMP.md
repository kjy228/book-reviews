# STOMP(Streaming Text Oriented Messagign Protocol)
- Spring only, Publish(topic/queue) $ Subscribe
- Sub Procotol over SockJs -> Custon Format for Message
- use stopmp js library

## Websocket
- socket over http(ws://... or wss://)
- full duplex, 2-way communication
- polling, long polling
    - polling : 요청을 보내고 응답이 와도 connect 끊지 않음
    - Long polling 양쪽다 connect 항시 맺어 놓고 있음
- auto-reconnect with intelligence
