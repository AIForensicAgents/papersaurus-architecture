# Security Model

## Authentication & Authorization

### Google Workspace Integration
- **Method**: Service Account with Domain-Wide Delegation
- **Key Storage**: Encrypted key file at `[SECURE_KEY_PATH]` on `[AWS_INSTANCE]`
- **Delegation**: JWT tokens generated per-request with user impersonation
- **Scopes**: Minimally scoped per operation (Drive, Docs, Sheets, Gmail, Calendar, Slides)

### API Key Management
- **Storage**: Google Cloud Secret Manager (Project: `[REDACTED_PROJECT_ID]`)
- **Keys Managed**:
  - `OPENAI_API_KEY` - OpenAI GPT-5/GPT-5-mini access
  - `GEMINI_API_KEY` - Google Gemini image generation
  - `SLACK_BOT_TOKEN` - Slack workspace integration
- **Access**: Retrieved at runtime via `getSecretText()`
- **Rotation**: Keys rotatable without code changes

### GitHub Integration
- **Method**: Personal Access Token (fine-grained)
- **Token**: `[GITHUB_PAT]`
- **Permissions**: Repository CRUD, contents read/write
- **Account**: `[GITHUB_USERNAME]`

## Data Protection

### PII Handling
- User emails used only for Google API delegation and response routing
- No PII stored in logs beyond operational necessity
- Drive files created with per-user folder isolation
- Public sharing links use "anyone with link" read-only access

### Key Masking in Code
- All API keys referenced via constants, never hardcoded in task code
- Service account paths abstracted behind auth utility functions
- GitHub tokens stored as module-level constants (not in task IIFEs)

## Network Security
- All API calls use HTTPS/TLS
- Timeout enforcement on all external requests (12-30 seconds)
- AbortController used for fetch timeout management
- No inbound ports exposed beyond SSH and application ports

## Error Handling
- Failed tasks trigger automatic retry (up to 3 attempts)
- Errors logged but sensitive data stripped from error messages
- `process.exit(1)` used for clean retry signaling

## Access Control
- Email list management includes admin-only operations
- Self-service limited to subscribe/unsubscribe own email
- Admin email: `[ADMIN_EMAIL]`
