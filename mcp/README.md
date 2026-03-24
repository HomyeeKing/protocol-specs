# MCP - Model Context Protocol

Model Context Protocol specification and data structures.

## Overview

MCP is a protocol for communication between AI models and external tools/resources.

## Base Types

```typescript
// JSON-RPC 2.0 Base
interface JSONRPCRequest {
  jsonrpc: "2.0";
  id: string | number;
  method: string;
  params?: object;
}

interface JSONRPCResponse {
  jsonrpc: "2.0";
  id: string | number;
  result?: object;
  error?: {
    code: number;
    message: string;
    data?: any;
  };
}
```

## Core Data Structures

### Resource

```typescript
interface Resource {
  uri: string;              // Resource identifier
  name: string;             // Human-readable name
  description?: string;     // Optional description
  mimeType?: string;        // Content type
  size?: number;            // Size in bytes
}

interface ResourceContents {
  uri: string;
  mimeType?: string;
  text?: string;            // For text resources
  blob?: string;            // For binary resources (base64)
}
```

### Tool

```typescript
interface Tool {
  name: string;
  description?: string;
  inputSchema: {
    type: "object";
    properties: Record<string, PropertySchema>;
    required?: string[];
  };
}

interface PropertySchema {
  type: "string" | "number" | "boolean" | "object" | "array";
  description?: string;
  enum?: string[];
  default?: any;
  items?: PropertySchema;   // For arrays
  properties?: Record<string, PropertySchema>; // For objects
}
```

### Prompt

```typescript
interface Prompt {
  name: string;
  description?: string;
  arguments?: PromptArgument[];
}

interface PromptArgument {
  name: string;
  description?: string;
  required?: boolean;
  default?: string;
}

interface PromptMessage {
  role: "user" | "assistant";
  content: PromptContent;
}

interface PromptContent {
  type: "text" | "image";
  text?: string;
  data?: string;            // Base64 for images
  mimeType?: string;
}
```

## Methods

### Initialization

```typescript
// Client → Server
interface InitializeRequest {
  method: "initialize";
  params: {
    protocolVersion: string;
    capabilities: ClientCapabilities;
    clientInfo: {
      name: string;
      version: string;
    };
  };
}

interface ClientCapabilities {
  experimental?: Record<string, object>;
  sampling?: object;
  roots?: {
    listChanged?: boolean;
  };
}

// Server → Client
interface InitializeResponse {
  result: {
    protocolVersion: string;
    capabilities: ServerCapabilities;
    serverInfo: {
      name: string;
      version: string;
    };
  };
}

interface ServerCapabilities {
  experimental?: Record<string, object>;
  logging?: object;
  prompts?: {
    listChanged?: boolean;
  };
  resources?: {
    subscribe?: boolean;
    listChanged?: boolean;
  };
  tools?: {
    listChanged?: boolean;
  };
}
```

### Resources

```typescript
// List resources
interface ListResourcesRequest {
  method: "resources/list";
  params?: {
    cursor?: string;
  };
}

interface ListResourcesResponse {
  result: {
    resources: Resource[];
    nextCursor?: string;
  };
}

// Read resource
interface ReadResourceRequest {
  method: "resources/read";
  params: {
    uri: string;
  };
}

interface ReadResourceResponse {
  result: {
    contents: ResourceContents[];
  };
}
```

### Tools

```typescript
// List tools
interface ListToolsRequest {
  method: "tools/list";
  params?: {
    cursor?: string;
  };
}

interface ListToolsResponse {
  result: {
    tools: Tool[];
    nextCursor?: string;
  };
}

// Call tool
interface CallToolRequest {
  method: "tools/call";
  params: {
    name: string;
    arguments?: Record<string, any>;
  };
}

interface CallToolResponse {
  result: {
    content: PromptContent[];
    isError?: boolean;
  };
}
```

### Prompts

```typescript
// List prompts
interface ListPromptsRequest {
  method: "prompts/list";
  params?: {
    cursor?: string;
  };
}

interface ListPromptsResponse {
  result: {
    prompts: Prompt[];
    nextCursor?: string;
  };
}

// Get prompt
interface GetPromptRequest {
  method: "prompts/get";
  params: {
    name: string;
    arguments?: Record<string, string>;
  };
}

interface GetPromptResponse {
  result: {
    description?: string;
    messages: PromptMessage[];
  };
}
```

## Notifications

```typescript
// Resource updated
interface ResourceUpdatedNotification {
  method: "notifications/resources/updated";
  params: {
    uri: string;
  };
}

// Tool list changed
interface ToolListChangedNotification {
  method: "notifications/tools/list_changed";
}

// Prompt list changed
interface PromptListChangedNotification {
  method: "notifications/prompts/list_changed";
}

// Logging
interface LoggingMessageNotification {
  method: "notifications/message";
  params: {
    level: "debug" | "info" | "notice" | "warning" | "error" | "critical";
    logger?: string;
    data: any;
  };
}
```

## Error Codes

```typescript
enum MCPErrorCodes {
  ParseError = -32700,
  InvalidRequest = -32600,
  MethodNotFound = -32601,
  InvalidParams = -32602,
  InternalError = -32603,
  ResourceNotFound = -32001,
  ToolNotFound = -32002,
  PromptNotFound = -32003,
}
```

## References

- [MCP Specification](https://modelcontextprotocol.io/)
- [MCP GitHub](https://github.com/modelcontextprotocol)
