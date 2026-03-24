┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                              PAPERSAURUS AI PLATFORM                                        │
│                         "Autonomous AI Document Intelligence"                               │
└─────────────────────────────────────────────────────────────────────────────────────────────┘


═══════════════════════════════════════════════════════════════════════════════════════════════
 LAYER 1 — INBOUND CHANNELS
═══════════════════════════════════════════════════════════════════════════════════════════════

  ┌──────────────────────┐          ┌──────────────────────┐       ┌──────────────────────┐
  │    📧 EMAIL / GMAIL  │          │    💬 SLACK BOT       │       │   🌐 HTTP WEBHOOK    │
  │    ──────────────────│          │    ──────────────────│       │   ──────────────────  │
  │                      │          │                      │       │                      │
  │  Gmail API (Push)    │          │  Events API          │       │  REST Endpoints      │
  │  Pub/Sub Trigger     │          │  Slash Commands      │       │  Cron Triggers       │
  │  Watch on INBOX      │          │  @mention Listener   │       │  Manual Invocations  │
  │  MIME Parsing        │          │  Thread Context      │       │  API Gateway          │
  │  Attachment Extract  │          │  Modal Submissions   │       │  Cloud Scheduler     │
  │                      │          │                      │       │                      │
  └──────────┬───────────┘          └──────────┬───────────┘       └──────────┬───────────┘
             │                                 │                              │
             │ (parsed message + metadata)     │ (event payload)             │ (HTTP request)
             │                                 │                              │
             ▼                                 ▼                              ▼
  ┌───────────────────────────────────────────────────────────────────────────────────────┐
  │                                                                                       │
  │                          🔐  AUTHENTICATION & SECURITY GATE                           │
  │                                                                                       │
  │   ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌───────────────┐   │
  │   │  JWT Validation  │  │ Sender Allow-   │  │ Rate Limiter    │  │ Input Sanitiz │   │
  │   │  & Token Verify  │  │ list / ACL      │  │ (per-user/chan) │  │ -ation Layer  │   │
  │   └─────────────────┘  └─────────────────┘  └─────────────────┘  └───────────────┘   │
  │                                                                                       │
  └──────────────────────────────────────┬────────────────────────────────────────────────┘
                                         │
                                         │  (authenticated, validated request)
                                         ▼

═══════════════════════════════════════════════════════════════════════════════════════════════
 LAYER 2 — CORE ORCHESTRATION ENGINE
═══════════════════════════════════════════════════════════════════════════════════════════════

  ┌───────────────────────────────────────────────────────────────────────────────────────┐
  │                                                                                       │
  │                    ⚙️  TASK DISPATCHER  (Node.js / Google Apps Script)                 │
  │                                                                                       │
  │   ┌─────────────────────────────────────────────────────────────────────────────┐     │
  │   │                        INTENT CLASSIFIER                                    │     │
  │   │                                                                             │     │
  │   │   Input ──▶ NLP Parse ──▶ Route to Handler                                 │     │
  │   │                                                                             │     │
  │   │   Detected Intents:                                                         │     │
  │   │   ├── "create document"    ──▶  DocGeneratorHandler                         │     │
  │   │   ├── "research topic"     ──▶  ResearchPipelineHandler                     │     │
  │   │   ├── "analyze data"       ──▶  DataAnalysisHandler                         │     │
  │   │   ├── "schedule meeting"   ──▶  CalendarHandler                             │     │
  │   │   ├── "translate content"  ──▶  TranslationHandler                          │     │
  │   │   ├── "generate slides"   ──▶  PresentationHandler                          │     │
  │   │   ├── "run code/query"    ──▶  CodeExecutionHandler                         │     │
  │   │   └── "general question"  ──▶  ConversationalHandler                        │     │
  │   └─────────────────────────────────────────────────────────────────────────────┘     │
  │                                                                                       │
  │   ┌─────────────────────────────────────────────────────────────────────────────┐     │
  │   │                     IIFE EXECUTION SANDBOX                                  │     │
  │   │                                                                             │     │
  │   │   (function() {                                                             │     │
  │   │     // Dynamic task composition                                             │     │
  │   │     const context  = buildContext(inbound);                                  │     │
  │   │     const plan     = aiPlanner.decompose(context);                           │     │
  │   │     const results  = await executeSteps(plan);                              │     │
  │   │     return formatOutput(results);                                            │     │
  │   │   })();                                                                     │     │
  │   │                                                                             │     │
  │   │   Features:                                                                 │     │
  │   │   • Multi-step task decomposition                                           │     │
  │   │   • Retry logic with exponential backoff                                    │     │
  │   │   • Parallel subtask execution                                              │     │
  │   │   • Context window management                                               │     │
  │   │   • Token budget allocation                                                 │     │
  │   └─────────────────────────────────────────────────────────────────────────────┘     │
  │                                                                                       │
  │          │                    │                     │                  │               │
  │          │ (AI prompt)        │ (API call)          │ (file op)       │ (compose)     │
  │          ▼                    ▼                     ▼                  ▼               │
  └───────────────────────────────────────────────────────────────────────────────────────┘
             │                    │                     │                  │
             ▼                    ▼                     ▼                  ▼

═══════════════════════════════════════════════════════════════════════════════════════════════
 LAYER 3 — AI PROVIDER MESH  &  EXTERNAL DATA SERVICES
═══════════════════════════════════════════════════════════════════════════════════════════════

  ┌─────────────────────────────────────────┐    ┌────────────────────────────────────────┐
  │                                         │    │                                        │
  │     🧠  AI PROVIDER ROUTER              │    │     🌍  EXTERNAL API CONNECTORS        │
  │                                         │    │                                        │
  │   ┌───────────┐  Model Selection:       │    │   ┌──────────────────────────────┐     │
  │   │           │  • Cost optimization    │    │   │  GDELT Project API            │     │
  │   │  Router   │  • Capability match     │    │   │  ─────────────────            │     │
  │   │  Logic    │  • Fallback chains      │    │   │  Global news & event data     │     │
  │   │           │  • Load balancing       │    │   │  Geopolitical analysis feeds   │     │
  │   └─────┬─────┘                         │    │   └──────────────────────────────┘     │
  │         │                               │    │                                        │
  │         ├──────────────────┐            │    │   ┌──────────────────────────────┐     │
  │         │                  │            │    │   │  Wikipedia / MediaWiki API    │     │
  │         ▼                  ▼            │    │   │  ─────────────────────────    │     │
  │   ┌───────────┐    ┌───────────┐       │    │   │  Encyclopedic references      │     │
  │   │  OpenAI   │    │ Anthropic │       │    │   │  Structured knowledge base    │     │
  │   │  ─────    │    │ ────────  │       │    │   └──────────────────────────────┘     │
  │   │           │    │           │       │    │                                        │
  │   │ GPT-4o    │    │ Claude 4  │       │    │   ┌──────────────────────────────┐     │
  │   │ GPT-4o    │    │ Claude    │       │    │   │  GitHub API                   │     │
  │   │  -mini    │    │  3.5      │       │    │   │  ──────────                   │     │
  │   │ o1/o3     │    │  Sonnet   │       │    │   │  Repository analysis          │     │
  │   │           │    │ Claude    │       │    │   │  Code review & PR data        │     │
  │   │ Embeddings│    │  3.5      │       │    │   │  Issue tracking context       │     │
  │   │ DALL·E    │    │  Haiku    │       │    │   └──────────────────────────────┘     │
  │   │ Whisper   │    │           │       │    │                                        │
  │   └───────────┘    └───────────┘       │    │   ┌──────────────────────────────┐     │
  │         │                              │    │   │  MusicBrainz API             │     │
  │         ▼                              │    │   │  ──────────────               │     │
  │   ┌───────────┐    ┌───────────┐       │    │   │  Music metadata & catalogs    │     │
  │   │  Google   │    │  Open /   │       │    │   └──────────────────────────────┘     │
  │   │  Gemini   │    │  Local    │       │    │                                        │
  │   │  ──────   │    │  ──────   │       │    │   ┌──────────────────────────────┐     │
  │   │           │    │           │       │    │   │  Additional Services          │     │
  │   │ Gemini    │    │ Ollama    │       │    │   │  ─────────────────────        │     │
  │   │  2.5 Pro  │    │ Mistral   │       │    │   │                              │     │
  │   │ Gemini    │    │ LLaMA     │       │    │   │  • Wolfram Alpha (math)       │     │
  │   │  2.5      │    │ Phi-4     │       │    │   │  • SEC/EDGAR (finance)        │     │
  │   │  Flash    │    │           │       │    │   │  • PubMed (medical)           │     │
  │   │           │    │ (self-    │       │    │   │  • arXiv (academic papers)    │     │
  │   │ Native    │    │  hosted)  │       │    │   │  • OpenWeather (weather)      │     │
  │   │ Workspace │    │           │       │    │   │  • NewsAPI (current events)   │     │
  │   │ Integr.   │    │           │       │    │   │  • Custom REST endpoints     │     │
  │   └───────────┘    └───────────┘       │    │   └──────────────────────────────┘     │
  │                                         │    │                                        │
  └─────────────────────┬───────────────────┘    └──────────────────┬─────────────────────┘
                        │                                           │
                        │  (AI-generated content,                   │  (enrichment data,
                        │   analysis, embeddings)                   │   facts, references)
                        │                                           │
                        └──────────────────┬────────────────────────┘
                                           │
                                           │  (merged results)
                                           ▼

═══════════════════════════════════════════════════════════════════════════════════════════════
 LAYER 4 — GOOGLE WORKSPACE INTEGRATION FABRIC
═══════════════════════════════════════════════════════════════════════════════════════════════

  ┌───────────────────────────────────────────────────────────────────────────────────────┐
  │                                                                                       │
  │              📁  GOOGLE WORKSPACE API LAYER  (Service Account + OAuth2)               │
  │                                                                                       │
  │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐  ┌──────────┐ │
  │   │             │  │             │  │             │  │              │  │          │ │
  │   │ Google      │  │ Google      │  │ Google      │  │ Google       │  │ Google   │ │
  │   │ Drive       │  │ Docs        │  │ Sheets      │  │ Slides       │  │Calendar  │ │
  │   │ ──────      │  │ ──────      │  │ ──────      │  │ ──────       │  │ ──────── │ │
  │   │             │  │             │  │             │  │              │  │          │ │
  │   │ • Create    │  │ • Create    │  │ • Create    │  │ • Create     │  │ • Create │ │
  │   │   files     │  │   docs      │  │   sheets    │  │   decks      │  │   events │ │
  │   │ • Upload    │  │ • Insert    │  │ • Read/     │  │ • Add        │  │ • Update │ │
  │   │   content   │  │   content   │  │   Write     │  │   slides     │  │   events │ │
  │   │ • Organize  │  │ • Format    │  │   cells     │  │ • Insert     │  │ • Query  │ │
  │   │   folders   │  │   text      │  │ • Charts    │  │   charts     │  │   free/  │ │
  │   │ • Share &   │  │ • Tables    │  │ • Formulas  │  │ • Format     │  │   busy   │ │
  │   │   permissions│ │ • Headers   │  │ • Pivot     │  │   layouts    │  │ • Send   │ │
  │   │ • Search    │  │ • Images    │  │   tables    │  │ • Speaker    │  │   invite │ │
  │   │ • Export    │  │ • Export    │  │ • Export    │  │   notes      │  │          │ │
  │   │   PDF/DOCX  │  │   PDF/MD   │  │   CSV/XLSX  │  │ • Export     │  │          │ │
  │   │             │  │             │  │             │  │   PDF/PPTX   │  │          │ │
  │   └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬───────┘  └────┬─────┘ │
  │          │                │                │                │               │       │
  └──────────┼────────────────┼────────────────┼────────────────┼───────────────┼───────┘
             │                │                │                │               │
             └────────────────┴────────────────┼────────────────┴───────────────┘
                                               │
                                               │  (created/modified artifacts)
                                               ▼

═══════════════════════════════════════════════════════════════════════════════════════════════
 LAYER 5 — OUTPUT CHANNELS & DELIVERY
═══════════════════════════════════════════════════════════════════════════════════════════════

  ┌───────────────────────────────────────────────────────────────────────────────────────┐
  │                         📤  OUTPUT FORMATTER & ROUTER                                 │
  │                                                                                       │
  │   ┌───────────────────────────────────────────────────────────────────────────────┐   │
  │   │  Response Composer                                                            │   │
  │   │  • Markdown → HTML rendering       • Token-aware truncation                   │   │
  │   │  • Citation & source linking        • Multi-part message splitting             │   │
  │   │  • Attachment bundling              • Format adaptation per channel            │   │
  │   └───────────────────────────────────────────────────────────────────────────────┘   │
  │                                                                                       │
  └────┬──────────────┬──────────────────┬───────────────────┬───────────────────┬────────┘
       │              │                  │                   │                   │
       ▼              ▼                  ▼                   ▼                   ▼
  ┌──────────┐  ┌──────────┐    ┌───────────────┐   ┌──────────────┐   ┌──────────────┐
  │  📧 Email │  │ 💬 Slack  │    │ 📁 Drive Files │   │ 🌐 Web Apps   │   │ 📊 Dashboards│
  │  Reply   │  │ Response │    │               │   │              │   │              │
  │  ─────── │  │ ──────── │    │  ───────────  │   │  ──────────  │   │  ──────────  │
  │          │  │          │    │               │   │              │   │              │
  │ Gmail    │  │ Channel  │    │ Docs with     │   │ Apps Script  │   │ Looker       │
  │ API      │  │ Messages │    │ formatted     │   │ Web Apps     │   │ Studio       │
  │ Send     │  │          │    │ content       │   │              │   │ Embeds       │
  │          │  │ Thread   │    │               │   │ HTML Service │   │              │
  │ Rich     │  │ Replies  │    │ Sheets with   │   │ deployed     │   │ Sheets-based │
  │ HTML     │  │          │    │ data &        │   │ endpoints    │   │ charts &     │
  │ Body     │  │ Block    │    │ charts        │   │              │   │ reports      │
  │          │  │ Kit UI   │    │               │   │ Interactive  │   │              │
  │ Inline   │  │          │    │ Slide decks   │   │ forms &      │   │ Auto-refresh │
  │ Attach-  │  │ Ephemeral│    │ auto-built    │   │ tools        │   │ data feeds   │
  │ ments    │  │ Messages │    │               │   │              │   │              │
  │          │  │          │    │ PDF exports   │   │ Public/      │   │              │
  │ CC/BCC   │  │ File     │    │ shared links  │   │ Private      │   │              │
  │ Support  │  │ Uploads  │    │               │   │ access       │   │              │
  └──────────┘  └──────────┘    └───────────────┘   └──────────────┘   └──────────────┘


═══════════════════════════════════════════════════════════════════════════════════════════════
 LAYER 6 — SECURITY, SECRETS & OBSERVABILITY
═══════════════════════════════════════════════════════════════════════════════════════════════

  ┌───────────────────────────────────────────────────────────────────────────────────────┐
  │                                                                                       │
  │            🔒  SECURITY & INFRASTRUCTURE LAYER  (Cross-Cutting Concerns)              │
  │                                                                                       │
  │   ┌──────────────────────┐   ┌──────────────────────┐   ┌──────────────────────────┐ │
  │   │  🔑 Secret Manager    │   │  🎫 Auth & Identity    │   │  👤 Service Accounts      │ │
  │   │  ───────────────────  │   │  ───────────────────  │   │  ────────────────────── │ │
  │   │                      │   │                      │   │                          │ │
  │   │  GCP Secret Manager  │   │  JWT Token Service   │   │  GCP SA (Workspace      │ │
  │   │  ──────────────────  │   │  ─────────────────   │   │   domain-wide deleg.)   │ │
  │   │                      │   │                      │   │                          │ │
  │   │  • API Keys:         │   │  • Issue signed JWT  │   │  • Gmail on behalf of   │ │
  │   │    ├─ OPENAI_KEY     │   │  • Verify incoming   │   │    user                 │ │
  │   │    ├─ ANTHROPIC_KEY  │   │    tokens             │   │  • Drive file access    │ │
  │   │    ├─ GEMINI_KEY     │   │  • Refresh token     │   │  • Calendar management  │ │
  │   │    ├─ SLACK_TOKEN    │   │    rotation           │   │  • Admin directory      │ │
  │   │    ├─ SLACK_SECRET   │   │  • Scope validation  │   │    lookups              │ │
  │   │    └─ GITHUB_TOKEN   │   │  • Session mgmt      │   │                          │ │
  │   │                      │   │                      │   │  Apps Script Properties  │ │
  │   │  • Credentials:      │   │  OAuth2 Flows        │   │  ────────────────────── │ │
  │   │    ├─ SA JSON keys   │   │  ─────────────       │   │  • Script Properties    │ │
  │   │    ├─ OAuth secrets  │   │  • 3-legged OAuth    │   │    (server-side)        │ │
  │   │    └─ Webhook sigs   │   │  • Service Account   │   │  • User Properties      │ │
  │   │                      │   │    impersonation     │   │    (per-user config)    │ │
  │   │  • Auto-rotation     │   │  • API key auth      │   │                          │ │
  │   │  • Audit logging     │   │  • HMAC verification │   │  • Encrypted at rest    │ │
  │   │  • Least privilege   │   │                      │   │  • Access-scoped        │ │
  │   └──────────────────────┘   └──────────────────────┘   └──────────────────────────┘ │
  │                                                                                       │
  │   ┌──────────────────────┐   ┌──────────────────────┐   ┌──────────────────────────┐ │
  │   │  📊 Logging &         │   │  🛡️ Rate Limiting &   │   │  🔍 Error Handling &     │ │
  │   │  Observability       │   │  Abuse Prevention    │   │  Recovery                │ │
  │   │  ───────────────────  │   │  ───────────────────  │   │  ────────────────────── │ │
  │   │                      │   │                      │   │                          │ │
  │   │  • Cloud Logging     │   │  • Per-user quotas   │   │  • Graceful degradation  │ │
  │   │  • Execution traces  │   │  • Per-channel caps  │   │  • AI provider fallback  │ │
  │   │  • Token usage       │   │  • Token budget      │   │  • Retry with backoff    │ │
  │   │    tracking          │   │    enforcement       │   │  • Dead letter queue     │ │
  │   │  • Cost allocation   │   │  • Spam detection    │   │  • User error messaging  │ │
  │   │  • Performance       │   │  • Cooldown periods  │   │  • Circuit breaker       │ │
  │   │    metrics           │   │                      │   │    pattern               │ │
  │   └──────────────────────┘   └──────────────────────┘   └──────────────────────────┘ │
  │                                                                                       │
  └───────────────────────────────────────────────────────────────────────────────────────┘