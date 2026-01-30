# @skillkit/mesh

[![npm version](https://img.shields.io/npm/v/@skillkit/mesh.svg)](https://www.npmjs.com/package/@skillkit/mesh)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

**Peer mesh network for SkillKit** - Multi-machine agent distribution with secure peer-to-peer communication.

## Installation

```bash
npm install @skillkit/mesh
```

## Key Features

- **Peer Discovery**: UDP multicast LAN discovery with signed announcements
- **Multi-Transport**: HTTP, WebSocket, and UDP transport layers
- **Ed25519 Cryptography**: Peer identity, message signing, and verification
- **XChaCha20-Poly1305**: End-to-end message encryption
- **TLS Support**: Self-signed certificates for secure transport
- **JWT Authentication**: Challenge-response handshake with EdDSA tokens
- **Health Monitoring**: Automatic peer health checks with latency tracking
- **Trust Management**: Peer trust/revoke with fingerprint-based verification

## Usage

### Initialize Mesh Network

```typescript
import { MeshHost, PeerIdentity, MeshSecurityConfig } from '@skillkit/mesh';

// Generate or load identity
const identity = await PeerIdentity.generate();
console.log('Fingerprint:', identity.fingerprint);

// Configure security
const security: MeshSecurityConfig = {
  discovery: { mode: 'signed' },
  transport: { encryption: 'required', tls: 'self-signed', requireAuth: true },
  trust: { autoTrustFirst: true },
};

// Create mesh host
const host = new MeshHost({
  hostId: 'my-workstation',
  identity,
  security,
});

await host.start();
```

### Discover Peers

```typescript
import { LocalDiscovery } from '@skillkit/mesh';

// Start discovery
const discovery = new LocalDiscovery({
  identity,
  port: 41234,
});

discovery.on('peer', (peer) => {
  console.log('Found peer:', peer.hostId, peer.fingerprint);
});

await discovery.start();
await discovery.announce();
```

### Send Messages

```typescript
// Send to a specific peer
await host.send('peer-fingerprint', {
  type: 'skill-sync',
  payload: { skills: ['react-patterns', 'api-design'] },
});

// Broadcast to all peers
await host.broadcast({
  type: 'announcement',
  payload: { message: 'New skill available' },
});
```

### Secure Transport

```typescript
import { SecureWebSocketTransport, TLSManager } from '@skillkit/mesh';

// Generate TLS certificate
const certInfo = await TLSManager.generateCertificate(identity, 'my-host');

// Create secure WebSocket transport
const transport = new SecureWebSocketTransport({
  port: 8443,
  tls: {
    cert: certInfo.cert,
    key: certInfo.key,
  },
  requireAuth: true,
});

await transport.listen();
```

### Trust Management

```typescript
import { SecureKeystore } from '@skillkit/mesh';

// Initialize keystore
const keystore = new SecureKeystore({
  path: '~/.skillkit/mesh/identity',
});

// Trust a peer
await keystore.addTrustedPeer(peerFingerprint, peerPublicKey);

// Revoke trust
await keystore.revokePeer(peerFingerprint);

// List trusted peers
const trusted = await keystore.getTrustedPeers();
```

## Security Levels

| Level | Discovery | Transport | Auth | Use Case |
|-------|-----------|-----------|------|----------|
| `development` | open | none | none | Local dev |
| `signed` | signed | optional | optional | Trusted LAN |
| `secure` (default) | signed | required | required | Production |
| `strict` | trusted-only | required | mTLS | High security |

## API Reference

### MeshHost

```typescript
interface MeshHost {
  start(): Promise<void>;
  stop(): Promise<void>;
  send(peerId: string, message: TransportMessage): Promise<void>;
  broadcast(message: TransportMessage): Promise<void>;
  getPeers(): PeerInfo[];
  on(event: 'message' | 'peer' | 'disconnect', handler: Function): void;
}
```

### PeerIdentity

```typescript
interface PeerIdentity {
  static generate(): Promise<PeerIdentity>;
  static fromPrivateKey(key: Uint8Array): Promise<PeerIdentity>;
  static load(path: string, passphrase?: string): Promise<PeerIdentity>;
  save(path: string, passphrase?: string): Promise<void>;
  sign(message: Uint8Array): Promise<Uint8Array>;
  static verify(sig: Uint8Array, msg: Uint8Array, pubKey: Uint8Array): Promise<boolean>;
  deriveSharedSecret(peerPublicKey: Uint8Array): Uint8Array;
  get publicKey(): Uint8Array;
  get fingerprint(): string;
}
```

### Types

```typescript
interface PeerInfo {
  hostId: string;
  fingerprint: string;
  address: string;
  port: number;
  status: 'online' | 'offline';
  latency?: number;
}

interface TransportMessage {
  type: string;
  payload: unknown;
  signature?: string;
  senderFingerprint?: string;
}

interface MeshSecurityConfig {
  discovery: { mode: 'open' | 'signed' | 'trusted-only' };
  transport: { encryption: 'none' | 'optional' | 'required'; tls: 'none' | 'self-signed' | 'ca-signed'; requireAuth: boolean };
  trust: { autoTrustFirst: boolean; requireManualApproval?: boolean; trustedFingerprints?: string[] };
}
```

## CLI Commands

```bash
skillkit mesh init            # Initialize mesh network
skillkit mesh add <address>   # Add a host to mesh
skillkit mesh remove <id>     # Remove a host
skillkit mesh list            # List known hosts
skillkit mesh discover        # Discover hosts on LAN
skillkit mesh health          # Check host health
skillkit mesh status          # Show mesh status

# Security
skillkit mesh security init   # Setup encryption keys
skillkit mesh security status # Show security status
skillkit mesh peer trust <id> # Trust a peer
skillkit mesh peer revoke <id># Revoke peer trust
skillkit mesh peer list       # List trusted peers
```

## Documentation

Full documentation: https://github.com/rohitg00/skillkit

## License

Apache-2.0
