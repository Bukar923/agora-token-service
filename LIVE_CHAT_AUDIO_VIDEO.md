# Live Chat Audio + Video Project Guide

This guide shows how to use the existing Agora token service in this repo to power a live chat experience that includes real-time audio/video (RTC) and optional messaging (RTM) or chat.

## 1) Start the token service

Configure environment variables and run the server:

```bash
cp .env.example .env
APP_ID=your_app_id APP_CERTIFICATE=your_app_certificate CORS_ALLOW_ORIGIN=http://localhost:3000 \
  go run cmd/main.go
```

You should see the service listening on port `8080`.

## 2) Generate an RTC token for audio/video

Use the `/getToken` endpoint to generate an RTC token for the user who will join the audio/video channel.

```bash
curl -X POST -H "Content-Type: application/json" -d '{
  "tokenType": "rtc",
  "channel": "live-room-01",
  "role": "publisher",
  "uid": "user-123",
  "expire": 3600
}' "http://localhost:8080/getToken"
```

The response contains the RTC token:

```json
{
  "token": "<rtc_token>"
}
```

Use this token with the Agora Web SDK (or another SDK) when joining the channel.

## 3) (Optional) Generate an RTM token for real-time messaging

If you need in-room messaging (e.g., text chat), generate an RTM token for the same user:

```bash
curl -X POST -H "Content-Type: application/json" -d '{
  "tokenType": "rtm",
  "uid": "user-123",
  "expire": 3600
}' "http://localhost:8080/getToken"
```

```json
{
  "token": "<rtm_token>"
}
```

## 4) (Optional) Generate a Chat token

If you use Agora Chat, you can request a chat token as well:

```bash
curl -X POST -H "Content-Type: application/json" -d '{
  "tokenType": "chat",
  "uid": "user-123",
  "expire": 3600
}' "http://localhost:8080/getToken"
```

```json
{
  "token": "<chat_token>"
}
```

## 5) Suggested client flow

1. Your client requests tokens from this service when a user joins the live room.
2. Use the RTC token to join the audio/video channel (`live-room-01`).
3. If needed, use the RTM or Chat tokens to connect to messaging services.

## 6) Tips

- Use unique channel names per live session (e.g., `live-room-2024-10-15`).
- Use unique user IDs per participant to avoid conflicts.
- Keep token expiration aligned with session length.

For more details on the token API, see the README.
