# **qbit**

*A tiny message queue that does one thing well*

## Philosophy
Message queues should be simple. If you need a 50-page manual to get started, you're using the wrong tool.

## Get Started in 60 Seconds
```bash
# Start the server
qbit serve --port 7380

# In another terminal - send a message
qbit send jobs '{"task": "process_image", "id": 123}'

# Receive messages
qbit recv jobs
# Output: {"task": "process_image", "id": 123}
```

## Why qbit?
- **Tiny**: 3MB binary, zero dependencies
- **Fast**: ~8,000 messages/second on modest hardware
- **Persistent**: Messages survive restarts
- **Simple**: Learn the API in 5 minutes

## Storage
Uses SQLite under the hood:
```
~/.qbit/
  ├── queues.db     # Message storage
  ├── jobs.queue    # Active messages
  └── jobs.dlq      # Failed messages
```

## Examples

### Background Job Processing
```bash
# Producer
qbit send images "resize:photo.jpg"

# Consumer
while true; do
  msg=$(qbit recv --wait images)
  [ -n "${msg}" ] && process_image "${msg}"
done
```

### Service Communication
```bash
# Service A - send event
qbit send events "user:login:123"

# Service B - process event
qbit recv events | while read event; do
  echo "Processing: ${event}"
  # ... handle event ...
done
```

## HTTP API
```bash
# Send message
curl -X POST http://localhost:7380/send/jobs \
  -d '{"task": "generate_report"}'

# Receive message  
curl http://localhost:7380/recv/jobs

# Queue stats
curl http://localhost:7380/stats/jobs
```

## Production Notes
- Run behind nginx for HTTPS
- Use systemd for process management
- Monitor disk space for message storage
- Set up log rotation

## When to Use Something Else
- Need clustering → Redis
- Need exactly-once delivery → Kafka
- Need complex routing → RabbitMQ
- Have enterprise requirements → AWS SQS

## Installation
```bash
# Linux
curl -s https://qbit.sh/install-linux | bash

# macOS
brew install qbit/tap/qbit

# Docker
docker run -p 7380:7380 qbit/server:latest
```

## Support
- Documentation: https://qbit.sh/docs
- Issues: https://github.com/qbit-mq/server/issues
- Email: help@qbit.sh

*Simple tools for complex problems.*
