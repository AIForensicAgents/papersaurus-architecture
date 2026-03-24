<!-- Papersaurus — AI-Powered Automation Platform: Orchestrating multi-provider AI, Google Workspace, and 30+ data APIs for end-to-end content generation, research, and communication automation. -->

<div align="center">

# 🦕 Papersaurus

### **AI-Powered Automation Platform**

*Orchestrating multi-provider AI, Google Workspace, and 30+ data APIs for end-to-end content generation, research, and communication automation.*

---

[![Node.js](https://img.shields.io/badge/Node.js-18%2B-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org/)
[![AWS EC2](https://img.shields.io/badge/AWS-EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/ec2/)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--5-412991?style=for-the-badge&logo=openai&logoColor=white)](https://openai.com/)
[![Anthropic](https://img.shields.io/badge/Anthropic-Claude%20Opus-D4A574?style=for-the-badge&logo=anthropic&logoColor=white)](https://anthropic.com/)
[![Google Gemini](https://img.shields.io/badge/Google-Gemini-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://deepmind.google/technologies/gemini/)
[![Google Workspace](https://img.shields.io/badge/Google-Workspace-34A853?style=for-the-badge&logo=google&logoColor=white)](https://workspace.google.com/)
[![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-brightgreen?style=for-the-badge)](CONTRIBUTING.md)

</div>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
  - [High-Level Architecture Diagram](#high-level-architecture-diagram)
  - [Execution Model](#execution-model)
- [Core Platform](#-core-platform)
  - [AI Orchestration](#ai-orchestration)
  - [Service Account Authentication](#service-account-authentication)
- [Communication Layer](#-communication-layer)
  - [Email Processing](#email-processing)
  - [Slack Integration](#slack-integration)
- [Google Workspace Integration](#-google-workspace-integration)
- [Image & Media Pipeline](#-image--media-pipeline)
- [Content Generation](#-content-generation)
- [Data & Research APIs](#-data--research-apis)
- [External Integrations](#-external-integrations)
- [Document Management](#-document-management)
- [Web Scraping & Research](#-web-scraping--research)
- [Utility Layer](#-utility-layer)
- [Infrastructure](#-infrastructure)
- [Security Considerations](#-security-considerations)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Configuration](#configuration)
  - [Running Your First Task](#running-your-first-task)
- [Usage Patterns](#-usage-patterns)
- [API Reference Summary](#-api-reference-summary)
- [Contributing](#-contributing)
- [License](#-license)
- [Credits & Acknowledgments](#-credits--acknowledgments)

---

## 🌐 Overview

**Papersaurus** is a comprehensive AI-powered automation platform designed to handle complex, multi-step workflows that span content generation, data research, communication, and document management. Built on Node.js 18+ and hosted on AWS EC2, it orchestrates multiple AI providers — OpenAI, Anthropic, and Google Gemini — through a unified dispatch layer, seamlessly integrating with the full Google Workspace suite and over 30 external data APIs.

### Key Capabilities

| Category | Capabilities |
|----------|-------------|
| **AI Orchestration** | Multi-provider prompt routing, structured JSON output, long-form generation, image synthesis |
| **Communication** | Automated email processing & reply, Slack bot integration, thread-aware messaging |
| **Content** | Recipes, educational docs, academic papers, web apps, coloring books, postcards, ads, QR codes |
| **Research** | Nutrition data, global news, clinical trials, music metadata, Wikipedia, web scraping |
| **Documents** | Google Docs/Sheets/Slides/Drive CRUD, AI-powered refactoring & review, PDF generation |
| **Media** | Image generation & editing, spectral analysis, video generation (Sora), compression |

---

## 🏗 Architecture

### High-Level Architecture Diagram