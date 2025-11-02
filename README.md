# lib-maia-do-client

Clean DigitalOcean GenAI API client for MAIA (Medical AI Assistant).

## Purpose

Extract DigitalOcean Knowledge Base, Agent, and Indexing job operations from the monolithic `maia-cloud-clean/server.js` into a simple, reusable library with no debug logging or complex state management.

## Features

- **Knowledge Base operations**: Create, list, get, update, delete KBs
- **Agent operations**: List, get, create, update, delete agents
- **Indexing operations**: Start indexing jobs, poll for completion
- **Simple API**: Promise-based functions with clear error handling
- **No dependencies on complex state**: Direct DO API calls only

## Installation

### Development (symlink)
```bash
# In this directory
npm link

# In your app
npm link lib-maia-do-client
```

### Production (npm)
```bash
npm install lib-maia-do-client
```

## Usage

```javascript
import { createKB, listKB, startIndexing } from 'lib-maia-do-client';

// Initialize with DO token
const doClient = new DigitalOceanClient(process.env.DIGITALOCEAN_TOKEN);

// Create a knowledge base
const kb = await doClient.createKB({
  name: 'my-kb',
  model: 'claude-sonnet-4.5',
  // ... other options
});

// Start indexing
const jobId = await doClient.startIndexing(kb.id, dataSourceId);

// Poll for completion
const status = await doClient.pollIndexingJob(jobId);
```

## Development

Extract from:
- `maia-cloud-clean/server.js` sections 6, 10 (KB lifecycle)
- `maia-cloud-clean/inventory/server-outline.md`

## Architecture

- **No cache manager**: Each call hits DO API directly
- **No background jobs**: Caller handles polling
- **No debug logging**: Production logging only (Winston INFO/WARN/ERROR)
- **Simple error handling**: Throw errors, let caller handle

## API Reference

```javascript
import { DigitalOceanClient } from 'lib-maia-do-client';

const doClient = new DigitalOceanClient(process.env.DIGITALOCEAN_TOKEN, {
  region: 'tor1',
  baseUrl: 'https://api.digitalocean.com'
});

// Knowledge Bases
const kb = await doClient.kb.create({
  name: 'my-kb',
  description: 'My knowledge base',
  model: 'claude-sonnet-4.5'
});

const kbs = await doClient.kb.list();
const kbDetails = await doClient.kb.get(kbId);

// Agents
const agents = await doClient.agent.list();
const agent = await doClient.agent.get(agentId);
const newAgent = await doClient.agent.create({
  name: 'my-agent',
  instructions: '...',
  model: 'claude-sonnet-4.5'
});

// Indexing
const jobId = await doClient.indexing.start(kbId, dataSourceUuids);
const status = await doClient.indexing.poll(kbId, jobId);
```

## Status

✅ **Complete** - Fully implemented and tested

