# ACP - Agent Communication Protocol

Agent Communication Protocol specification and data structures.

## Overview

ACP is a protocol for communication between AI agents and between agents and humans.

## Base Types

```typescript
// Message envelope
interface Message {
  id: string;
  type: MessageType;
  sender: AgentIdentity;
  recipient: AgentIdentity | "broadcast";
  timestamp: string;        // ISO 8601
  content: MessageContent;
  metadata?: MessageMetadata;
}

interface AgentIdentity {
  id: string;
  name: string;
  type: AgentType;
  capabilities: string[];
  endpoint?: string;
}

enum AgentType {
  HUMAN = "human",
  AI_AGENT = "ai_agent",
  SERVICE = "service",
  HYBRID = "hybrid",
}

enum MessageType {
  REQUEST = "request",
  RESPONSE = "response",
  NOTIFICATION = "notification",
  ERROR = "error",
}
```

## Message Content

### Request

```typescript
interface RequestContent {
  action: string;
  parameters?: Record<string, any>;
  context?: Context;
  constraints?: Constraint[];
  priority?: Priority;
  timeout?: number;        // milliseconds
}

interface Context {
  conversationId?: string;
  parentId?: string;
  threadId?: string;
  workspace?: string;
  tags?: string[];
}

interface Constraint {
  type: "time" | "resource" | "permission" | "dependency";
  value: any;
  required: boolean;
}

enum Priority {
  LOW = "low",
  NORMAL = "normal",
  HIGH = "high",
  URGENT = "urgent",
}
```

### Response

```typescript
interface ResponseContent {
  requestId: string;
  status: ResponseStatus;
  result?: any;
  error?: ErrorResponse;
  metadata?: ResponseMetadata;
}

enum ResponseStatus {
  SUCCESS = "success",
  PARTIAL = "partial",
  FAILED = "failed",
  CANCELLED = "cancelled",
  TIMEOUT = "timeout",
}

interface ErrorResponse {
  code: string;
  message: string;
  details?: any;
  recoverable: boolean;
}

interface ResponseMetadata {
  duration?: number;
  tokensUsed?: number;
  confidence?: number;
  sources?: string[];
}
```

### Notification

```typescript
interface NotificationContent {
  event: string;
  data?: any;
  scope: "private" | "team" | "public";
}
```

## Agent Capabilities

```typescript
interface AgentCapabilities {
  // Communication
  messaging: {
    supported: boolean;
    protocols: string[];
    maxMessageSize?: number;
  };
  
  // Task execution
  taskExecution: {
    supported: boolean;
    taskTypes: string[];
    maxConcurrentTasks?: number;
  };
  
  // Knowledge
  knowledge: {
    supported: boolean;
    domains: string[];
    canLearn: boolean;
    canShare: boolean;
  };
  
  // Tools
  tools: {
    supported: boolean;
    availableTools: ToolDefinition[];
  };
  
  // Memory
  memory: {
    supported: boolean;
    types: MemoryType[];
    retention?: number;    // days
  };
  
  // Collaboration
  collaboration: {
    supported: boolean;
    canDelegate: boolean;
    canCollaborate: boolean;
    canSupervise: boolean;
  };
}

interface ToolDefinition {
  name: string;
  description: string;
  inputSchema: JSONSchema;
  outputSchema: JSONSchema;
}

interface JSONSchema {
  type: string;
  properties?: Record<string, JSONSchema>;
  required?: string[];
  items?: JSONSchema;
}

enum MemoryType {
  SHORT_TERM = "short_term",
  LONG_TERM = "long_term",
  EPISODIC = "episodic",
  SEMANTIC = "semantic",
  PROCEDURAL = "procedural",
}
```

## Task Protocol

### Task Definition

```typescript
interface Task {
  id: string;
  type: string;
  status: TaskStatus;
  definition: TaskDefinition;
  assignment?: TaskAssignment;
  progress?: TaskProgress;
  result?: TaskResult;
  createdAt: string;
  updatedAt: string;
}

interface TaskDefinition {
  goal: string;
  description?: string;
  requirements: Requirement[];
  constraints: Constraint[];
  successCriteria: SuccessCriteria[];
  estimatedDuration?: number;
  priority: Priority;
}

interface Requirement {
  id: string;
  description: string;
  type: "functional" | "non-functional";
  mandatory: boolean;
}

interface SuccessCriteria {
  id: string;
  description: string;
  measurable: boolean;
  metric?: string;
  targetValue?: any;
}

enum TaskStatus {
  PENDING = "pending",
  QUEUED = "queued",
  IN_PROGRESS = "in_progress",
  BLOCKED = "blocked",
  COMPLETED = "completed",
  FAILED = "failed",
  CANCELLED = "cancelled",
}

interface TaskAssignment {
  assignedTo: string;
  assignedBy: string;
  assignedAt: string;
  deadline?: string;
}

interface TaskProgress {
  percentage: number;
  currentStep?: string;
  totalSteps?: number;
  completedSteps?: number;
  lastUpdate: string;
}

interface TaskResult {
  output?: any;
  artifacts?: Artifact[];
  metrics?: Record<string, any>;
  lessonsLearned?: string[];
}

interface Artifact {
  id: string;
  type: string;
  name: string;
  uri?: string;
  metadata?: Record<string, any>;
}
```

### Task Messages

```typescript
// Task creation
interface CreateTaskRequest {
  action: "task/create";
  parameters: {
    task: TaskDefinition;
    assignTo?: string;
  };
}

// Task status update
interface TaskStatusUpdate {
  event: "task/status_changed";
  data: {
    taskId: string;
    oldStatus: TaskStatus;
    newStatus: TaskStatus;
    reason?: string;
  };
}

// Task progress update
interface TaskProgressUpdate {
  event: "task/progress";
  data: {
    taskId: string;
    progress: TaskProgress;
  };
}

// Task result
interface TaskCompleted {
  event: "task/completed";
  data: {
    taskId: string;
    result: TaskResult;
  };
}

// Task delegation
interface DelegateTaskRequest {
  action: "task/delegate";
  parameters: {
    taskId: string;
    delegateTo: string;
    reason?: string;
    context?: any;
  };
}
```

## Collaboration Protocol

### Team Formation

```typescript
interface Team {
  id: string;
  name: string;
  members: TeamMember[];
  roles: TeamRole[];
  objectives: string[];
  createdAt: string;
}

interface TeamMember {
  agentId: string;
  role: string;
  joinedAt: string;
  responsibilities: string[];
}

interface TeamRole {
  name: string;
  description: string;
  permissions: string[];
  responsibilities: string[];
}
```

### Coordination Messages

```typescript
// Request for collaboration
interface CollaborationRequest {
  action: "collaboration/request";
  parameters: {
    type: CollaborationType;
    description: string;
    requiredCapabilities: string[];
    duration?: number;
    compensation?: any;
  };
}

enum CollaborationType {
  COOPERATION = "cooperation",     // Working together
  COORDINATION = "coordination",    // Coordinating actions
  NEGOTIATION = "negotiation",      // Negotiating terms
  DELEGATION = "delegation",        // Delegating tasks
}

// Negotiation
interface NegotiationRequest {
  action: "negotiation/start";
  parameters: {
    topic: string;
    proposal: any;
    constraints: Constraint[];
    deadline?: string;
  };
}

interface CounterProposal {
  action: "negotiation/counter";
  parameters: {
    negotiationId: string;
    proposal: any;
    rationale?: string;
  };
}

interface NegotiationAgreement {
  action: "negotiation/accept";
  parameters: {
    negotiationId: string;
    terms: any;
  };
}
```

## Knowledge Sharing

### Knowledge Exchange

```typescript
interface KnowledgeShare {
  action: "knowledge/share";
  parameters: {
    knowledge: KnowledgeItem;
    accessLevel: AccessLevel;
    recipients?: string[];
  };
}

interface KnowledgeItem {
  id: string;
  type: KnowledgeType;
  content: any;
  metadata: KnowledgeMetadata;
}

enum KnowledgeType {
  FACT = "fact",
  PROCEDURE = "procedure",
  CONCEPT = "concept",
  EXPERIENCE = "experience",
  MODEL = "model",
}

interface KnowledgeMetadata {
  source: string;
  confidence: number;
  timestamp: string;
  version: string;
  tags?: string[];
  relatedKnowledge?: string[];
}

enum AccessLevel {
  PUBLIC = "public",
  TEAM = "team",
  PRIVATE = "private",
  RESTRICTED = "restricted",
}

// Knowledge query
interface KnowledgeQuery {
  action: "knowledge/query";
  parameters: {
    query: string;
    filters?: KnowledgeFilters;
    limit?: number;
  };
}

interface KnowledgeFilters {
  types?: KnowledgeType[];
  minConfidence?: number;
  tags?: string[];
  dateRange?: {
    from: string;
    to: string;
  };
}
```

## Error Handling

```typescript
enum ACPErrorCodes {
  // Standard errors
  INVALID_MESSAGE = "INVALID_MESSAGE",
  UNSUPPORTED_ACTION = "UNSUPPORTED_ACTION",
  AGENT_UNREACHABLE = "AGENT_UNREACHABLE",
  TIMEOUT = "TIMEOUT",
  
  // Authentication/Authorization
  UNAUTHORIZED = "UNAUTHORIZED",
  FORBIDDEN = "FORBIDDEN",
  EXPIRED_CREDENTIALS = "EXPIRED_CREDENTIALS",
  
  // Task errors
  TASK_NOT_FOUND = "TASK_NOT_FOUND",
  TASK_ALREADY_ASSIGNED = "TASK_ALREADY_ASSIGNED",
  TASK_DEPENDENCY_FAILED = "TASK_DEPENDENCY_FAILED",
  
  // Resource errors
  RESOURCE_EXHAUSTED = "RESOURCE_EXHAUSTED",
  CAPACITY_EXCEEDED = "CAPACITY_EXCEEDED",
  
  // Collaboration errors
  COLLABORATION_REJECTED = "COLLABORATION_REJECTED",
  NEGOTIATION_FAILED = "NEGOTIATION_FAILED",
  TEAM_FULL = "TEAM_FULL",
}

interface ACPError {
  code: ACPErrorCodes;
  message: string;
  details?: {
    field?: string;
    value?: any;
    reason?: string;
  };
  recoverable: boolean;
  suggestedAction?: string;
}
```

## Security

```typescript
interface SecurityContext {
  authentication: AuthenticationInfo;
  authorization: AuthorizationInfo;
  encryption: EncryptionInfo;
}

interface AuthenticationInfo {
  method: "token" | "certificate" | "oauth" | "api_key";
  token?: string;
  expiresAt?: string;
  scopes: string[];
}

interface AuthorizationInfo {
  permissions: string[];
  restrictions: Restriction[];
}

interface Restriction {
  type: "rate_limit" | "resource" | "action";
  value: any;
}

interface EncryptionInfo {
  algorithm: string;
  keyExchange: string;
  integrityCheck: string;
}
```

## References

- 🌐 [Official Website](https://agentcommunicationprotocol.dev/)
- 📚 [ACP Documentation](https://agentcommunicationprotocol.dev/introduction/welcome)
- 💻 [ACP GitHub](https://github.com/agentcommunicationprotocol)
