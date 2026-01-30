# @skillkit/messaging

[![npm version](https://img.shields.io/npm/v/@skillkit/messaging.svg)](https://www.npmjs.com/package/@skillkit/messaging)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

**Inter-agent messaging for SkillKit** - Asynchronous communication between AI agents across the mesh network.

## Installation

```bash
npm install @skillkit/messaging
```

## Key Features

- **Agent-to-Agent Messaging**: Send messages between AI coding agents
- **Inbox/Outbox Pattern**: Persistent message storage with read tracking
- **Message Threading**: Reply chains and conversation threads
- **Priority Levels**: Urgent, normal, and low priority messages
- **Mesh Integration**: Built on @skillkit/mesh for secure delivery
- **Offline Queuing**: Messages queued when recipient offline
- **Message Archiving**: Archive old messages for reference

## Usage

### Initialize Messaging

```typescript
import { MessagingClient } from '@skillkit/messaging';
import { MeshHost } from '@skillkit/mesh';

// Create messaging client (requires mesh host)
const messaging = new MessagingClient({
  meshHost: host,
  storagePath: '~/.skillkit/messages',
});

await messaging.init();
```

### Send Messages

```typescript
// Send a message to another agent
await messaging.send({
  to: 'claude@laptop',
  subject: 'Code review completed',
  body: 'I reviewed the authentication module. See attached suggestions.',
  priority: 'normal',
  attachments: [{ type: 'diff', content: '...' }],
});

// Reply to a message
await messaging.reply(messageId, {
  body: 'Thanks! I will apply the suggestions.',
});
```

### Read Inbox

```typescript
// Get inbox messages
const inbox = await messaging.getInbox({
  unreadOnly: false,
  limit: 20,
});

// Get unread count
const unreadCount = await messaging.getUnreadCount();

// Read a specific message
const message = await messaging.read(messageId);

// Mark as read
await messaging.markAsRead(messageId);
```

### Message Management

```typescript
// Archive a message
await messaging.archive(messageId);

// Get sent messages
const sent = await messaging.getSent({ limit: 10 });

// Delete a message
await messaging.delete(messageId);

// Search messages
const results = await messaging.search('authentication');
```

### Event Handling

```typescript
// Listen for new messages
messaging.on('message', (msg) => {
  console.log('New message from:', msg.from);
  console.log('Subject:', msg.subject);
});

// Listen for delivery confirmations
messaging.on('delivered', (msgId) => {
  console.log('Message delivered:', msgId);
});
```

## API Reference

### MessagingClient

```typescript
interface MessagingClient {
  init(): Promise<void>;
  send(message: OutgoingMessage): Promise<string>;
  reply(messageId: string, reply: ReplyMessage): Promise<string>;
  getInbox(options?: InboxOptions): Promise<Message[]>;
  getSent(options?: SentOptions): Promise<Message[]>;
  read(messageId: string): Promise<Message>;
  markAsRead(messageId: string): Promise<void>;
  archive(messageId: string): Promise<void>;
  delete(messageId: string): Promise<void>;
  search(query: string): Promise<Message[]>;
  getUnreadCount(): Promise<number>;
  on(event: 'message' | 'delivered' | 'error', handler: Function): void;
  close(): Promise<void>;
}
```

### Types

```typescript
interface Message {
  id: string;
  from: string;
  to: string;
  subject: string;
  body: string;
  priority: 'urgent' | 'normal' | 'low';
  timestamp: Date;
  read: boolean;
  archived: boolean;
  threadId?: string;
  attachments?: Attachment[];
}

interface OutgoingMessage {
  to: string;
  subject: string;
  body: string;
  priority?: 'urgent' | 'normal' | 'low';
  threadId?: string;
  attachments?: Attachment[];
}

interface Attachment {
  type: 'file' | 'diff' | 'skill' | 'code';
  name?: string;
  content: string;
}

interface InboxOptions {
  unreadOnly?: boolean;
  limit?: number;
  offset?: number;
  since?: Date;
}
```

## CLI Commands

```bash
skillkit message send         # Send message to agent
skillkit message inbox        # View inbox
skillkit message read <id>    # Read a message
skillkit message reply <id>   # Reply to message
skillkit message archive <id> # Archive message
skillkit message sent         # View sent messages
skillkit message status       # Messaging status
```

## Documentation

Full documentation: https://github.com/rohitg00/skillkit

## License

Apache-2.0
