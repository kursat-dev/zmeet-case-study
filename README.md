<div align="center">

# 🎥 zmeet

**Secure, Real-Time Meeting & Collaboration Platform**

[![Status](https://img.shields.io/badge/Status-Case_Study-blue.svg)]()
[![License: Private](https://img.shields.io/badge/License-Proprietary-red.svg)]()
[![Next.js](https://img.shields.io/badge/Next.js-16.0-black.svg)](https://nextjs.org/)
[![Socket.io](https://img.shields.io/badge/Socket.io-4.0-white.svg)](https://socket.io/)

[Overview](#-overview) • [Architecture](#-system-architecture) • [Features](#-key-features) • [Security](#-security--privacy) • [AI Integration](#-ai-voice-assistant-experiment)

</div>

---

## 📖 Overview

**zmeet** is a modern, high-performance video conferencing and collaboration platform designed to bridge the gap between casual social connection and professional meeting tools. 

This repository serves as a **technical case study** of the platform's architecture. It demonstrates how to build a scalable, real-time application using modern web technologies while handling complex challenges like low-latency signaling, field-level encryption for privacy, and hybrid AI integration.

> **Note:** This repository is for architectural demonstration purposes. Source code, API keys, and sensitive configurations have been omitted.

## 🏗 System Architecture

The system utilizes a **Hybrid Architecture** combining serverless scalability with stateful real-time performance.

### High-Level Design

```mermaid
graph TD
    Client[Client (Next.js + React 19)]
    
    subgraph "Application Layer"
        Next[Next.js App Router]
        Socket[Custom Node.js/Socket.io Server]
    end
    
    subgraph "Data & Storage"
        DB[(MongoDB)]
        Redis[(Redis/Cache)]
    end
    
    subgraph "External Services"
        Stream[Stream.io (WebRTC/AV)]
        Groq[Groq AI (Llama 3)]
        Resend[Resend (Email)]
    end

    Client -->|http/rest| Next
    Client -->|ws/wss| Socket
    Client -->|rtp/dtls| Stream
    
    Next --> DB
    Next --> Groq
    Next --> Resend
    
    Socket --> DB
    Socket --> Redis
```

### Core Components

1.  **Frontend**: Built with **Next.js 16** (App Router) and **React 19**, utilizing `zustand` for client state and `tanstack-query` for server state management.
2.  **Signaling Server**: A custom **Node.js + Socket.io** server handles real-time presence, peer signaling, and instant notifications, decoupled from the stateless Next.js handlers.
3.  **Media Server**: Offloaded to **Stream.io** global edge network for high-fidelity, low-latency audio/video transmission, ensuring scalability without managing raw TURN/STUN infrastructure.
4.  **Database**: **MongoDB** with Mongoose ORM, featuring a custom field-level encryption layer for PII protection.

## ✨ Key Features

- **⚡ Real-Time Communication**: Seamless video/audio calls with screen sharing and recording capabilities.
- **🔐 Privacy-First Identity**: End-to-end encrypted user profile fields (Bio, Location, Email) stored securely at rest.
- **💬 Rich Chat**: Integrated text chat with channel support, threads, and file sharing.
- **👥 Social Graph**: Friend requests, user blocking, and granular privacy controls.
- **📅 Smart Scheduling**: Meeting scheduler with timezone awareness using `luxon`.
- **🤖 Hybrid AI Assistant**: Context-aware meeting assistant powered by Llama 3 via Groq.

## 🛡 Security & Privacy

Security was a primary driver in the architectural design, moving beyond standard practices to implement defense-in-depth strategies.

### 1. Field-Level Encryption
Instead of relying solely on disk encryption, sensitive user data is encrypted at the application level before database insertion.
*   **Technique**: AES-256 encryption using `crypto-js`.
*   **Protected Fields**: `email`, `fullName`, `bio`, `location`.
*   **Searchability**: Deterministic hashing (`sha256`) allows looking up users by email without decrypting the entire database.

### 2. Authentication & Session Management
*   **Dual Token System**: Short-lived Access Tokens (JWT) paired with secure httpOnly Refresh Tokens.
*   **Token Rotation**: Automatic rotation detects and blocks reused refresh tokens to prevent replay attacks.
*   **Middleware**: Custom Next.js middleware enforces route protection and role-based access control (RBAC).

### 3. Isolation
*   **Auth Boundary**: Authentication logic is isolated in a dedicated `(auth)` route group.
*   **Validation**: Strict input validation using `zod` schemas prevents NoSQL injection and malformed data attacks.

## 🤖 AI Voice Assistant (Experiment)

An experimental feature exploring **low-latency voice interactions** within meetings.

*   **Objective**: Provide a "Sales & Marketing Expert" assistant that can answer context-aware questions during a call.
*   **Architecture**:
    *   **LLM Provider**: Groq (Llama 3-8b) for sub-second inference speeds.
    *   **Context Injection**: The active meeting context (topic, participants) is dynamically injected into the system prompt.
    *   **Fallback**: A local regular-expression engine provides instant responses for common queries if the API is unreachable or rate-limited.

## 🚀 Development Workflow

- **Monorepo-style Structure**: Frontend and Backend logic coexist in `src` for tight type safety and rapid iteration.
- **Type Safety**: strict TypeScript configuration across the entire stack.
- **CI/CD**: Automated linting (`eslint`) and type checking on commit.

## 💡 Lessons Learned & Trade-offs

### Building a Custom Signaling Server vs. Serverless
*   **Challenge**: Next.js Serverless functions are stateless, making persistent WebSocket connections difficult.
*   **Solution**: We opted for a custom `server.js` entry point. While this breaks "pure" serverless deployment on some platforms, it provides the persistent connection required for reliable real-time signaling at a fraction of the cost of managed WebSocket services.

### Application-Level Encryption
*   **Trade-off**: Encrypting fields like "Name" makes fuzzy search and sorting computationally expensive or impossible directly in the DB.
*   **Resolution**: We implemented a "Search Hash" strategy for exact matches and accepted the trade-off that fuzzy search would require a specialized search index (e.g., ElasticSearch) if scaled further.

---

<div align="center">

**[View Portfolio](https://github.com/kursat-dev)** • **[Contact Me](mailto:urensukursat@gmail.com)**

</div>
