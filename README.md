# 🦕 Papersaurus Architecture

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Node.js 18+](https://img.shields.io/badge/Node.js-18%2B-339933?logo=nodedotjs&logoColor=white)](https://nodejs.org/)
[![Anthropic Claude](https://img.shields.io/badge/Claude-Opus%204-cc785c?logo=anthropic&logoColor=white)](https://www.anthropic.com/)
[![OpenAI GPT-5](https://img.shields.io/badge/GPT--5-OpenAI-412991?logo=openai&logoColor=white)](https://openai.com/)
[![Google Gemini](https://img.shields.io/badge/Gemini-Google-4285F4?logo=google&logoColor=white)](https://deepmind.google/technologies/gemini/)

> **Multi-model AI orchestration platform** that processes requests via Email (Gmail API) and Slack, executes tasks using a rich utility library of 60+ globally available functions, and delivers results back through the original channel.

---

## Overview

Papersaurus is a production AI orchestration system that transforms natural-language requests — received through **Email** or **Slack** — into executable JavaScript code, runs that code against a comprehensive library of 60+ utility functions, and returns the results to the user through whichever channel they used to reach out.

At its core, Papersaurus leverages **multiple AI models in concert**: Claude Opus 4 for deep reasoning and code generation, GPT-5 for structured JSON outputs and classification, Google Gemini for image generation and editing, and OpenAI Sora for video creation. Each model is selected dynamically based on the nature of the task — a principle we call **"right model for the right job."**

The system runs on an AWS EC2 instance under Node.js 18+ using CommonJS modules. Every task is encapsulated in a single **async IIFE (Immediately Invoked Function Expression)** that has access to all utility functions as globals — no imports, no filesystem dependencies, no setup. This architecture makes the system remarkably predictable: each task is self-contained, stateless, and retryable.

### What Can Papersaurus Do?

- 📧 Read and reply to emails with AI-generated, richly formatted responses
- 📊 Create Google Docs, Sheets, and Slides from scratch
- 🖼️ Generate, edit, and analyze images using multiple AI models
- 🎬 Generate videos with reference image support via Sora
- 📰 Research topics across Wikipedia, GDELT, ClinicalTrials.gov, USDA, and more
- 🌐 Build and host self-contained web applications
- 📅 Schedule meetings by finding open calendar slots
- 🧬 Perform multi-source research with automatic summarization
- 📮 Generate printable USPS-compliant postcards
- 🎨 Create personalized kids' coloring books
- 🍕 Generate complete recipe pages with nutrition data
- 🏗️ Manage GitHub repositories programmatically
- ...and much more

---

## System Architecture Flow

Every interaction with Papersaurus follows the same six-stage pipeline, regardless of whether the request arrives via email or Slack:

### Stage 1: Inbound Message Arrival
A message arrives through one of two channels:
- **Email** — The Gmail API polls for new messages in the monitored inbox. New emails (and replies within existing threads) are detected and queued for processing.
- **Slack** — A Slack webhook receives messages directed at the Papersaurus bot (via mention or direct message). Thread context, channel metadata, and any attached files are captured.

### Stage 2: Message Parsing & Context Extraction
The raw inbound message is parsed to extract all relevant context:
- **Sender identity** (email address or Slack user ID)
- **Thread/conversation history** (prior messages in the email thread or Slack thread)
- **Channel metadata** (Gmail thread ID, Slack channel ID, timestamp)
- **Attachments** (images, documents, files — downloaded and made available for processing)
- **Reply-to information** (so the response routes back correctly)

### Stage 3: AI Planning Layer
The parsed request and its full context are sent to the **primary reasoning model** (Claude Opus 4 or GPT-5, depending on task characteristics). The AI model:
- Interprets the user's intent
- Determines which utility functions are needed
- Plans the execution strategy (sequential vs. parallel operations)
- Generates a complete, self-contained JavaScript IIFE

### Stage 4: IIFE Code Generation
The AI produces a single async IIFE that:
- Calls the necessary global utility functions
- Handles data transformations and intermediate processing
- Constructs the final output (text, documents, images, etc.)
- Sends the response back via the appropriate channel function (`sendEmail` or `sendSlackMessage`)

All 60+ utility functions are available as **globals** — no `require()` or `import` statements needed.

### Stage 5: Runtime Execution
The generated IIFE is executed in the **Node.js 18+ runtime**:
- The code runs within the global context where all utilities are pre-loaded
- Google API authentication is handled transparently via JWT service accounts
- External API calls are made as needed (USDA, GDELT, Wikipedia, etc.)
- Files are uploaded to Google Drive and public sharing links are generated
- If execution fails, `process.exit(1)` triggers an **automatic retry** (up to 3 attempts)

### Stage 6: Result Delivery
The results are delivered back through the **originating channel**:
- **Email responses** are sent as threaded replies in the original Gmail conversation, with proper HTML formatting, inline images, and file attachments via Google Drive links
- **Slack responses** are posted as threaded replies in the original Slack thread, with rich formatting, file uploads, and interactive elements

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        PAPERSAURUS ARCHITECTURE                        │
└─────────────────────────────────────────────────────────────────────────┘

  ┌──────────┐    ┌──────────┐
  │  Gmail   │    │  Slack   │
  │  Inbox   │    │ Webhook  │
  └────┬─────┘    └────┬─────┘
       │               │
       ▼               ▼
  ┌────────────────────────────────────────────────────────────────────┐
  │                     📨 MESSAGE INGESTION LAYER                     │
  │                                                                    │
  │   • Poll Gmail API / Receive Slack webhooks                        │
  │   • Extract sender, thread, channel, attachments                   │
  │   • Build conversation context from thread history                 │
  └──────────────────────────┬─────────────────────────────────────────┘
                             │
                             ▼
  ┌────────────────────────────────────────────────────────────────────┐
  │                     🧠 AI PLANNING LAYER                           │
  │                                                                    │
  │   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │
  │   │ Claude Opus 4│  │   GPT-5 /    │  │   Gemini /   │            │
  │   │  (Reasoning  │  │  GPT-5-mini  │  │    Sora      │            │
  │   │   & Code)    │  │ (Structured) │  │  (Media)     │            │
  │   └──────┬───────┘  └──────┬───────┘  └──────┬───────┘            │
  │          │                 │                  │                     │
  │          └────────┬────────┘──────────────────┘                    │
  │                   │                                                │
  │          Interpret intent → Select functions → Generate IIFE       │
  └──────────────────────────┬─────────────────────────────────────────┘
                             │
                             ▼
  ┌────────────────────────────────────────────────────────────────────┐
  │                   ⚡ EXECUTION ENGINE (Node.js 18+)                │
  │                                                                    │
  │   ┌────────────────────────────────────────────────────────────┐   │
  │   │  (async () => {                                            │   │
  │   │    // All 60+ utility functions available as globals       │   │
  │   │    const data = await runPrompt({ ... });                  │   │
  │   │    const doc  = await createGoogleDocFromHTML(html);       │   │
  │   │    const img  = await generateImage("a cute dinosaur");   │   │
  │   │    await sendEmail({ to, subject, body, attachments });    │   │
  │   │  })();                                                     │   │
  │   └────────────────────────────────────────────────────────────┘   │
  │                                                                    │
  │   Retry on failure: process.exit(1) → up to 3 retries             │
  └──────────────────────────┬─────────────────────────────────────────┘
                             │
                             ▼
  ┌────────────────────────────────────────────────────────────────────┐
  │                  🔧 UTILITY FUNCTION LIBRARY (60+)                 │
  │                                                                    │
  │  ┌─────────┐ ┌──────────┐ ┌─────────┐ ┌──────────┐ ┌──────────┐  │
  │  │   AI &  │ │ Document │ │ Image & │ │  Data &  │ │ Infra &  │  │
  │  │Prompting│ │Generation│ │  Media  │ │ Research │ │  DevOps  │  │
  │  └────┬────┘ └────┬─────┘ └────┬────┘ └────┬─────┘ └────┬─────┘  │
  │       │           │            │            │             │        │
  │       ▼           ▼            ▼            ▼             ▼        │
  │  runPrompt   createDoc    generateImage  gdeltData   createRepo   │
  │              createSlides editImage      wikiQuery    csvToSheet   │
  │              writeArticle createVideo    fdcNutrition genWebApp    │
  │              genRecipe    buildPostcard  clinTrials   findSlot     │
  │              ...          ...            ...          ...          │
  └──────────────────────────┬─────────────────────────────────────────┘
                             │
                             ▼
  ┌────────────────────────────────────────────────────────────────────┐
  │                  🔐 EXTERNAL SERVICES & APIs                       │
  │                                                                    │
  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐              │
  │  │  Google  │ │  GitHub  │ │  Slack   │ │ Research │              │
  │  │ (Drive,  │ │   API    │ │   API    │ │   APIs   │              │
  │  │  Docs,   │ │          │ │          │ │ (USDA,   │              │
  │  │ Sheets,  │ │          │ │          │ │  GDELT,  │              │
  │  │ Slides,  │ │          │ │          │ │  Wiki,   │              │
  │  │ Gmail,   │ │          │ │          │ │  NIH...) │              │
  │  │Calendar) │ │          │ │          │ │          │              │
  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘              │
  └──────────────────────────┬─────────────────────────────────────────┘
                             │
                             ▼
  ┌────────────────────────────────────────────────────────────────────┐
  │                   📤 RESPONSE DELIVERY                             │
  │                                                                    │
  │   Channel-aware routing: reply goes back the way it came           │
  │                                                                    │
  │   ┌──────────────────┐          ┌──────────────────┐               │
  │   │  📧 Gmail Reply  │          │  💬 Slack Reply   │               │
  │   │  • Threaded       │          │  • Threaded       │               │
  │   │  • HTML formatted │          │  • Rich formatting│               │
  │   │  • Drive links    │          │  • File uploads   │               │
  │   │  • Inline images  │          │  • Block Kit UI   │               │
  │   └──────────────────┘          └──────────────────┘               │
  └────────────────────────────────────────────────────────────────────┘
```

---

## Core AI Models

Papersaurus orchestrates multiple AI models, selecting the optimal model for each sub-task based on its strengths:

### 🟠 Claude Opus 4 (Anthropic)
**Role:** Primary reasoning engine, code generation, long-form document creation

Claude Opus 4 serves as the **backbone of the planning layer**. It is responsible for:

- **Request interpretation** — Understanding complex, multi-step user requests from natural language
- **IIFE code generation** — Producing the executable JavaScript code that orchestrates utility function calls
- **Long-form document creation** — Writing comprehensive reports, articles, and educational content
- **Recursive section building** — Constructing large documents section-by-section, passing the full accumulated context to each subsequent section call to maintain coherence, narrative flow, and consistent terminology across 10,000+ word documents
- **Complex reasoning** — Multi-step planning, error recovery strategies, and decision-making about which tools and models to use for sub-tasks

Claude Opus 4's extended thinking capabilities and large context window make it ideal for tasks that require maintaining coherence across large bodies of text or reasoning through complex multi-step workflows.

### 🟣 GPT-5 / GPT-5-mini (OpenAI)
**Role:** Structured JSON output, keyword extraction, classification, schema-validated responses

GPT-5 is used wherever the system needs **deterministic, schema-validated structured output**:

- **Structured JSON generation** — Using OpenAI's native structured output mode with JSON schemas to guarantee valid, parseable responses every time
- **Keyword extraction** — Pulling key terms, entities, and topics from unstructured text
- **Classification** — Categorizing content, detecting intent, labeling data
- **Lightweight reasoning** — Tasks where speed matters more than depth (GPT-5-mini)
- **Data transformation** — Converting between formats, normalizing data structures

The structured output capability is critical for tasks like generating recipe nutrition JSON, extracting event metadata, or producing classification labels that downstream code must parse reliably.

### 🔵 Gemini (Google)
**Role:** Image generation and editing

Google's Gemini image models power all visual content creation:

- **Image generation** — Creating images from text descriptions with high fidelity
- **Image editing** — Modifying existing images based on natural language instructions (add elements, change colors, apply styles)
- **Reference-based generation** — Creating new images that maintain stylistic consistency with provided reference images
- **Multi-turn image refinement** — Iterative editing workflows where each edit builds on the previous result

### 🎬 OpenAI Sora
**Role:** Video generation

Sora handles all video creation tasks:

- **Text-to-video generation** — Creating short video clips from text descriptions
- **