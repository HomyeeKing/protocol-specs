# LSP - Language Server Protocol

Language Server Protocol specification and data structures.

## Overview

LSP is a protocol for communication between code editors/IDEs and language servers.

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
  error?: ResponseError;
}

interface ResponseError {
  code: number;
  message: string;
  data?: any;
}
```

### Basic Types

```typescript
// Position in a document
interface Position {
  line: number;           // 0-based
  character: number;      // 0-based
}

// Range in a document
interface Range {
  start: Position;
  end: Position;
}

// Location in a document
interface Location {
  uri: string;
  range: Range;
}

// Text document identifier
interface TextDocumentIdentifier {
  uri: string;
  version?: number;
}

// Text document position
interface TextDocumentPositionParams {
  textDocument: TextDocumentIdentifier;
  position: Position;
}
```

## Initialization

### Client → Server

```typescript
interface InitializeRequest {
  method: "initialize";
  params: {
    processId: number | null;
    clientInfo?: {
      name: string;
      version?: string;
    };
    locale?: string;
    rootPath?: string | null;
    rootUri: string | null;
    capabilities: ClientCapabilities;
    initializationOptions?: any;
    trace?: "off" | "messages" | "verbose";
    workspaceFolders?: WorkspaceFolder[] | null;
  };
}

interface ClientCapabilities {
  textDocument?: TextDocumentClientCapabilities;
  workspace?: WorkspaceClientCapabilities;
  window?: WindowClientCapabilities;
  general?: GeneralClientCapabilities;
  experimental?: Record<string, any>;
}

interface TextDocumentClientCapabilities {
  synchronization?: {
    dynamicRegistration?: boolean;
    willSave?: boolean;
    willSaveWaitUntil?: boolean;
    didSave?: boolean;
  };
  completion?: {
    dynamicRegistration?: boolean;
    completionItem?: {
      snippetSupport?: boolean;
      commitCharactersSupport?: boolean;
      documentationFormat?: MarkupKind[];
      deprecatedSupport?: boolean;
      preselectSupport?: boolean;
      tagSupport?: { valueSet: CompletionItemTag[] };
      insertReplaceSupport?: boolean;
      resolveSupport?: { properties: string[] };
      insertTextModeSupport?: { valueSet: InsertTextMode[] };
    };
    completionItemKind?: { valueSet: CompletionItemKind[] };
  };
  hover?: {
    dynamicRegistration?: boolean;
    contentFormat?: MarkupKind[];
  };
  signatureHelp?: {
    dynamicRegistration?: boolean;
    signatureInformation?: {
      documentationFormat?: MarkupKind[];
      parameterInformation?: {
        labelOffsetSupport?: boolean;
      };
      activeParameterSupport?: boolean;
    };
  };
  declaration?: {
    dynamicRegistration?: boolean;
    linkSupport?: boolean;
  };
  definition?: {
    dynamicRegistration?: boolean;
    linkSupport?: boolean;
  };
  typeDefinition?: {
    dynamicRegistration?: boolean;
    linkSupport?: boolean;
  };
  implementation?: {
    dynamicRegistration?: boolean;
    linkSupport?: boolean;
  };
  references?: {
    dynamicRegistration?: boolean;
  };
  documentHighlight?: {
    dynamicRegistration?: boolean;
  };
  documentSymbol?: {
    dynamicRegistration?: boolean;
    symbolKind?: {
      valueSet: SymbolKind[];
    };
    hierarchicalDocumentSymbolSupport?: boolean;
    tagSupport?: { valueSet: SymbolTag[] };
    labelSupport?: boolean;
  };
  codeAction?: {
    dynamicRegistration?: boolean;
    codeActionLiteralSupport?: {
      codeActionKind: { valueSet: CodeActionKind[] };
    };
    isPreferredSupport?: boolean;
    disabledSupport?: boolean;
    dataSupport?: boolean;
    resolveSupport?: { properties: string[] };
    honorsChangeAnnotations?: boolean;
  };
  codeLens?: {
    dynamicRegistration?: boolean;
  };
  formatting?: {
    dynamicRegistration?: boolean;
  };
  rangeFormatting?: {
    dynamicRegistration?: boolean;
  };
  onTypeFormatting?: {
    dynamicRegistration?: boolean;
  };
  rename?: {
    dynamicRegistration?: boolean;
    prepareSupport?: boolean;
    honorsChangeAnnotations?: boolean;
  };
  documentLink?: {
    dynamicRegistration?: boolean;
    tooltipSupport?: boolean;
  };
  colorProvider?: {
    dynamicRegistration?: boolean;
  };
  foldingRange?: {
    dynamicRegistration?: boolean;
    rangeLimit?: number;
    lineFoldingOnly?: boolean;
    foldingRangeKind?: { valueSet: FoldingRangeKind[] };
    foldingRange?: {
      collapsedText?: boolean;
    };
  };
  publishDiagnostics?: {
    relatedInformation?: boolean;
    tagSupport?: { valueSet: DiagnosticTag[] };
    versionSupport?: boolean;
    codeDescriptionSupport?: boolean;
    dataSupport?: boolean;
  };
  selectionRange?: {
    dynamicRegistration?: boolean;
  };
  callHierarchy?: {
    dynamicRegistration?: boolean;
  };
  semanticTokens?: {
    dynamicRegistration?: boolean;
    requests: {
      range?: boolean | { optional?: boolean };
      full?: boolean | { delta?: boolean };
    };
    tokenTypes: string[];
    tokenModifiers: string[];
    formats: TokenFormat[];
    overlappingTokenSupport?: boolean;
    multilineTokenSupport?: boolean;
    serverCancelSupport?: boolean;
    augmentsSyntaxTokens?: boolean;
  };
  linkedEditingRange?: {
    dynamicRegistration?: boolean;
  };
  moniker?: {
    dynamicRegistration?: boolean;
  };
  typeHierarchy?: {
    dynamicRegistration?: boolean;
  };
  inlineValue?: {
    dynamicRegistration?: boolean;
  };
  inlayHint?: {
    dynamicRegistration?: boolean;
    resolveSupport?: { properties: string[] };
  };
  diagnostic?: {
    dynamicRegistration?: boolean;
    relatedDocumentSupport?: boolean;
  };
}

interface ServerCapabilities {
  textDocumentSync?: TextDocumentSyncOptions | TextDocumentSyncKind;
  completionProvider?: CompletionOptions;
  hoverProvider?: boolean | HoverOptions;
  signatureHelpProvider?: SignatureHelpOptions;
  declarationProvider?: boolean | DeclarationOptions | DeclarationRegistrationOptions;
  definitionProvider?: boolean | DefinitionOptions;
  typeDefinitionProvider?: boolean | TypeDefinitionOptions | TypeDefinitionRegistrationOptions;
  implementationProvider?: boolean | ImplementationOptions | ImplementationRegistrationOptions;
  referencesProvider?: boolean | ReferenceOptions;
  documentHighlightProvider?: boolean | DocumentHighlightOptions;
  documentSymbolProvider?: boolean | DocumentSymbolOptions;
  codeActionProvider?: boolean | CodeActionOptions;
  codeLensProvider?: CodeLensOptions;
  documentLinkProvider?: DocumentLinkOptions;
  colorProvider?: boolean | DocumentColorOptions | DocumentColorRegistrationOptions;
  workspaceSymbolProvider?: boolean | WorkspaceSymbolOptions;
  documentFormattingProvider?: boolean | DocumentFormattingOptions;
  documentRangeFormattingProvider?: boolean | DocumentRangeFormattingOptions;
  documentOnTypeFormattingProvider?: DocumentOnTypeFormattingOptions;
  renameProvider?: boolean | RenameOptions;
  foldingRangeProvider?: boolean | FoldingRangeOptions | FoldingRangeRegistrationOptions;
  executeCommandProvider?: ExecuteCommandOptions;
  selectionRangeProvider?: boolean | SelectionRangeOptions | SelectionRangeRegistrationOptions;
  linkedEditingRangeProvider?: boolean | LinkedEditingRangeOptions | LinkedEditingRangeRegistrationOptions;
  callHierarchyProvider?: boolean | CallHierarchyOptions | CallHierarchyRegistrationOptions;
  semanticTokensProvider?: SemanticTokensOptions | SemanticTokensRegistrationOptions;
  monikerProvider?: boolean | MonikerOptions | MonikerRegistrationOptions;
  typeHierarchyProvider?: boolean | TypeHierarchyOptions | TypeHierarchyRegistrationOptions;
  inlineValueProvider?: boolean | InlineValueOptions | InlineValueRegistrationOptions;
  inlayHintProvider?: boolean | InlayHintOptions | InlayHintRegistrationOptions;
  diagnosticProvider?: DiagnosticOptions | DiagnosticRegistrationOptions;
  inlineCompletionProvider?: boolean | InlineCompletionOptions;
  workspace?: {
    workspaceFolders?: {
      supported?: boolean;
      changeNotifications?: string | boolean;
    };
    fileOperations?: {
      didCreate?: FileOperationRegistrationOptions;
      willCreate?: FileOperationRegistrationOptions;
      didRename?: FileOperationRegistrationOptions;
      willRename?: FileOperationRegistrationOptions;
      didDelete?: FileOperationRegistrationOptions;
      willDelete?: FileOperationRegistrationOptions;
    };
  };
  experimental?: Record<string, any>;
}
```

### Server → Client

```typescript
interface InitializeResponse {
  result: {
    capabilities: ServerCapabilities;
    serverInfo?: {
      name: string;
      version?: string;
    };
  };
}
```

## Text Synchronization

```typescript
// Document opened
interface DidOpenTextDocumentParams {
  textDocument: TextDocumentItem;
}

interface TextDocumentItem {
  uri: string;
  languageId: string;
  version: number;
  text: string;
}

// Document changed
interface DidChangeTextDocumentParams {
  textDocument: VersionedTextDocumentIdentifier;
  contentChanges: TextDocumentContentChangeEvent[];
}

interface VersionedTextDocumentIdentifier {
  uri: string;
  version: number;
}

interface TextDocumentContentChangeEvent {
  range?: Range;
  rangeLength?: number;
  text: string;
}

// Document saved
interface DidSaveTextDocumentParams {
  textDocument: TextDocumentIdentifier;
  text?: string;
}

// Document closed
interface DidCloseTextDocumentParams {
  textDocument: TextDocumentIdentifier;
}
```

## Language Features

### Completion

```typescript
interface CompletionParams extends TextDocumentPositionParams {
  context?: CompletionContext;
}

interface CompletionContext {
  triggerKind: CompletionTriggerKind;
  triggerCharacter?: string;
}

enum CompletionTriggerKind {
  Invoked = 1,
  TriggerCharacter = 2,
  TriggerForIncompleteCompletions = 3,
}

interface CompletionItem {
  label: string | CompletionItemLabelDetails;
  labelDetails?: CompletionItemLabelDetails;
  kind?: CompletionItemKind;
  tags?: CompletionItemTag[];
  detail?: string;
  documentation?: string | MarkupContent;
  deprecated?: boolean;
  preselect?: boolean;
  sortText?: string;
  filterText?: string;
  insertText?: string;
  insertTextFormat?: InsertTextFormat;
  insertTextMode?: InsertTextMode;
  textEdit?: TextEdit | InsertReplaceEdit;
  textEditText?: string;
  additionalTextEdits?: TextEdit[];
  commitCharacters?: string[];
  command?: Command;
  data?: any;
  resolveSupport?: {
    properties: string[];
  };
}

interface CompletionList {
  isIncomplete: boolean;
  items: CompletionItem[];
}
```

### Hover

```typescript
interface HoverParams extends TextDocumentPositionParams {}

interface Hover {
  contents: MarkupContent | MarkedString | MarkedString[];
  range?: Range;
}

interface MarkupContent {
  kind: MarkupKind;
  value: string;
}

type MarkedString = string | {
  language: string;
  value: string;
};

enum MarkupKind {
  PlainText = "plaintext",
  Markdown = "markdown",
}
```

### Definition

```typescript
interface DefinitionParams extends TextDocumentPositionParams {}

// Response can be:
type Definition = Location | Location[] | LocationLink[];

interface LocationLink {
  originSelectionRange?: Range;
  targetUri: string;
  targetRange: Range;
  targetSelectionRange?: Range;
}
```

### Diagnostics

```typescript
interface PublishDiagnosticsParams {
  uri: string;
  version?: number;
  diagnostics: Diagnostic[];
}

interface Diagnostic {
  range: Range;
  severity?: DiagnosticSeverity;
  code?: string | number;
  codeDescription?: CodeDescription;
  source?: string;
  message: string;
  tags?: DiagnosticTag[];
  relatedInformation?: DiagnosticRelatedInformation[];
  data?: any;
}

enum DiagnosticSeverity {
  Error = 1,
  Warning = 2,
  Information = 3,
  Hint = 4,
}

interface DiagnosticRelatedInformation {
  location: Location;
  message: string;
}
```

### Document Symbols

```typescript
interface DocumentSymbolParams {
  textDocument: TextDocumentIdentifier;
}

interface DocumentSymbol {
  name: string;
  detail?: string;
  kind: SymbolKind;
  tags?: SymbolTag[];
  deprecated?: boolean;
  range: Range;
  selectionRange: Range;
  children?: DocumentSymbol[];
}

enum SymbolKind {
  File = 1,
  Module = 2,
  Namespace = 3,
  Package = 4,
  Class = 5,
  Method = 6,
  Property = 7,
  Field = 8,
  Constructor = 9,
  Enum = 10,
  Interface = 11,
  Function = 12,
  Variable = 13,
  Constant = 14,
  String = 15,
  Number = 16,
  Boolean = 17,
  Array = 18,
  Object = 19,
  Key = 20,
  Null = 21,
  EnumMember = 22,
  Struct = 23,
  Event = 24,
  Operator = 25,
  TypeParameter = 26,
}
```

## Workspace

```typescript
interface WorkspaceFolder {
  uri: string;
  name: string;
}

interface WorkspaceEdit {
  changes?: { [uri: string]: TextEdit[] };
  documentChanges?: (TextDocumentEdit | CreateFile | RenameFile | DeleteFile)[];
  changeAnnotations?: {
    [id: string]: ChangeAnnotation;
  };
}

interface TextEdit {
  range: Range;
  newText: string;
}

interface TextDocumentEdit {
  textDocument: OptionalVersionedTextDocumentIdentifier;
  edits: (TextEdit | AnnotatedTextEdit)[];
}

interface CreateFile {
  kind: "create";
  uri: string;
  options?: {
    overwrite?: boolean;
    ignoreIfExists?: boolean;
  };
  annotationId?: string;
}

interface RenameFile {
  kind: "rename";
  oldUri: string;
  newUri: string;
  options?: {
    overwrite?: boolean;
    ignoreIfExists?: boolean;
  };
  annotationId?: string;
}

interface DeleteFile {
  kind: "delete";
  uri: string;
  options?: {
    recursive?: boolean;
    ignoreIfNotExists?: boolean;
  };
  annotationId?: string;
}
```

## Error Codes

```typescript
enum LSPErrorCodes {
  ParseError = -32700,
  InvalidRequest = -32600,
  MethodNotFound = -32601,
  InvalidParams = -32602,
  InternalError = -32603,
  ServerNotInitialized = -32002,
  UnknownErrorCode = -32001,
  RequestCancelled = -32800,
  ContentModified = -32801,
  ServerCancelled = -32802,
}
```

## References

- 🌐 [Official Specification](https://microsoft.github.io/language-server-protocol/)
- 💻 [LSP GitHub](https://github.com/microsoft/language-server-protocol)
- 📖 [LSP Documentation](https://code.visualstudio.com/api/language-extensions/language-server-extension-guide)
- 🧪 [LSP Inspector](https://github.com/usernamehw/vscode-lsp-inspector)
