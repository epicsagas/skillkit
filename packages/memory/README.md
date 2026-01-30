# @skillkit/memory

[![npm version](https://img.shields.io/npm/v/@skillkit/memory.svg)](https://www.npmjs.com/package/@skillkit/memory)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

**Semantic memory with embeddings for SkillKit** - CozoDB-backed persistent memory with vector search for AI agents.

## Installation

```bash
npm install @skillkit/memory
```

## Key Features

- **CozoDB Backend**: Embedded graph database with HNSW vector index
- **Semantic Search**: Cosine similarity search over embeddings
- **Xenova Transformers**: Local embedding generation (no API keys)
- **Observations & Learnings**: Store raw observations and compressed learnings
- **Memory Compression**: Extract patterns from observations
- **Persistent Storage**: SQLite-backed durability
- **Memory Reinforcement**: Boost memory relevance through usage

## Usage

### Basic Memory Operations

```typescript
import { MemoryStore, EmbeddingEncoder } from '@skillkit/memory';

// Initialize encoder
const encoder = new EmbeddingEncoder();
await encoder.init();

// Create memory store
const store = new MemoryStore('./my-project/.skillkit/memory');
await store.init();

// Store an observation
await store.addObservation({
  content: 'User prefers TypeScript strict mode',
  tags: ['typescript', 'preferences'],
  source: 'conversation',
});

// Search memories
const results = await store.search('typescript configuration', { limit: 5 });
```

### Memory Compression

```typescript
import { MemoryCompressor } from '@skillkit/memory';

// Compress observations into learnings
const compressor = new MemoryCompressor(store);
const learnings = await compressor.compress({
  minObservations: 3,
  maxAge: 7 * 24 * 60 * 60 * 1000, // 7 days
});
```

### Vector Search

```typescript
// Get embedding for a query
const embedding = await encoder.encode('React best practices');

// Search by vector
const results = await store.searchByVector(embedding, {
  limit: 10,
  threshold: 0.7,
});
```

### Export Memories as Skills

```typescript
import { MemoryExporter } from '@skillkit/memory';

// Export memories to a skill file
const exporter = new MemoryExporter(store);
const skill = await exporter.toSkill({
  name: 'project-patterns',
  tags: ['patterns', 'best-practices'],
});
```

## API Reference

### MemoryStore

```typescript
interface MemoryStore {
  init(): Promise<void>;
  addObservation(obs: Observation): Promise<string>;
  addLearning(learning: Learning): Promise<string>;
  search(query: string, options?: SearchOptions): Promise<Memory[]>;
  searchByVector(embedding: number[], options?: SearchOptions): Promise<Memory[]>;
  reinforce(id: string): Promise<void>;
  close(): Promise<void>;
}
```

### EmbeddingEncoder

```typescript
interface EmbeddingEncoder {
  init(): Promise<void>;
  encode(text: string): Promise<number[]>;
  encodeBatch(texts: string[]): Promise<number[][]>;
  dispose(): Promise<void>;
}
```

### Types

```typescript
interface Observation {
  content: string;
  tags?: string[];
  source?: string;
  metadata?: Record<string, unknown>;
}

interface Learning {
  title: string;
  content: string;
  tags?: string[];
  confidence?: number;
}

interface Memory {
  id: string;
  content: string;
  embedding: number[];
  score?: number;
  createdAt: Date;
}
```

## Documentation

Full documentation: https://github.com/rohitg00/skillkit

## License

Apache-2.0
