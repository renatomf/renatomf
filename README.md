<div align="left">

<img src="https://raw.githubusercontent.com/renatomf/renatomf/main/github-asc-3.png" alt="Renato Marques - Terminal Profile" />

<br>
<br>

## Conheça minhas redes e saiba mais sobre meu perfil. 

<b>Site Pessoal:</b> <a href="https://rmf-dev.com.br/" target="_blank" rel="noopener noreferrer">
rmf-dev.com.br </a> <br>

<b>LinkedIn:</b> <a href="https://www.linkedin.com/in/renatomf/" target="_blank" rel="noopener noreferrer">
linkedin.com/in/renatomf </a>

</div>

---

# Projetos & Arquiteturas

Projetos organizados por domínio técnico, arquitetura e principais desafios de engenharia.

---

## AI Engineering

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[CodeDriven](https://github.com/renatomf/nextjs-codedriven)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 16` · `React 19` · `AI SDK` · `Groq` · `Tree-sitter` · `pgvector` · `Drizzle` · `Neon` · `Vercel Workflows` · `Auth.js` · `Stripe` · `Vitest` · `Playwright` | Modular Monolith + Clean Architecture e DDD seletivos + Durable Workflows | Static analysis · RAG · LLM evals · architecture rules no CI · testes em Postgres real · durable execution | Auditor de codebases JS/TS: importa um repositório (GitHub App ou ZIP), combina regras determinísticas com revisão por LLM (achado sem evidência é descartado), gera nota por categoria e oferece chat com o código via RAG. Arquitetura detalhada abaixo. |
| **[Echo](https://github.com/renatomf/nextjs-echo)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 15` · `Turborepo` · `Convex` · `Clerk` · `Vapi` · `Jotai` · `Zod` · `Sentry` | Modular Monorepo + Realtime AI | AI agents · RAG · tool calling · multi-tenancy · human handoff | Plataforma de atendimento com widget embeddable, dashboard multi-tenant e agente de IA capaz de consultar conhecimento, usar ferramentas e escalar para atendimento humano. |
| **[CraftAI](https://github.com/renatomf/nextjs-craftAI)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 15` · `tRPC` · `Prisma` · `Clerk` · `E2B` · `Inngest` · `Agent Kit` · `Zod` | Agentic Workflow + Isolated Sandbox | Agent orchestration · runtime isolation · code generation · live preview · rate limiting | Gerador de aplicações por IA que transforma linguagem natural em código, executa o resultado em sandbox isolado e disponibiliza preview ao vivo. |
| **[Polaris](https://github.com/renatomf/nextjs-polaris)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 16` · `CodeMirror 6` · `Vercel AI SDK` · `Gemini` · `Convex` · `Inngest` · `Firecrawl` · `Clerk` · `Sentry` | AI IDE + Reactive Backend + Async Jobs | Agent orchestration · code editing · background processing · web context · reactive state | IDE web com editor de código, agente de IA e aquisição de contexto externo, mantendo operações interativas separadas de jobs assíncronos. |
| **[PlayForge](https://github.com/renatomf/nextjs-playforge)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 16` · `React 19` · `Vercel AI SDK` · `Trigger.dev` · `Drizzle` · `Neon` · `Clerk` · `Sentry` · `Tailwind CSS` | AI Platform + Async Workflows + Multi-Provider LLM | AI orchestration · multi-provider models · background jobs · observability · AI workflows | Plataforma de AI Engineering construída com Next.js, combinando múltiplos provedores de LLM, workflows assíncronos, persistência, autenticação e observabilidade em uma arquitetura orientada a produção. |
| **[Meet AI](https://github.com/renatomf/nextjs-meet-ai)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 15` · `tRPC` · `Drizzle` · `Neon` · `Better Auth` · `Stream Video` · `Stream Chat` · `OpenAI Realtime` · `Inngest` | Realtime Communication + Async AI Processing | Realtime media · AI agents · async jobs · context processing · summarization | Plataforma SaaS de reuniões em que agentes de IA participam em tempo real e geram posteriormente resumos estruturados e contexto pesquisável. |

<details>
<summary><b>CodeDriven — arquitetura em detalhe</b></summary>

<br>

**Monólito modular** ([ADR-001](https://github.com/renatomf/nextjs-codedriven/blob/main/docs/decisions/001-modular-monolith.md)): um único deploy Next.js, com o domínio dividido em seis módulos — `identity`, `projects`, `ingestion`, `analysis`, `chat` e `billing`. Microsserviços foram avaliados e descartados: o gargalo medido (análise dentro da request) se resolveu com execução durável, sem separar o sistema.

**Clean Architecture seletiva.** Camadas só onde há regra de negócio; telas sem regra (settings, dashboard) não ganham entidades nem repositórios.

```
src/modules/<módulo>/
├── domain/          # TypeScript puro: sem banco, sem framework, sem process.env
├── application/     # use cases + portas (interfaces) que eles usam
├── infrastructure/  # adaptadores: Drizzle, Stripe, pgvector, ONNX
├── index.ts         # API pública pura (importável até no client)
└── server.ts        # API pública de servidor: composition root ("server-only")
```

- **Portas e adaptadores com critério:** uma porta só existe com duas implementações ou um fake de teste (`Embedder`, `VectorStore`, `BillingRepository`). Stripe, com uma implementação só, fica sem porta.
- **Injeção de dependência por factory:** os use cases recebem as dependências (`createQuota({ repo, catalog, now })`), inclusive o relógio; o `server.ts` monta tudo com o executor certo (conexão ou transação).
- **Regras de arquitetura no CI** com dependency-cruiser (baseline de violações: 0): domínio puro, application sem infraestrutura, acesso a módulos só pela API pública, `src/app` sem acesso ao banco, sem React nos módulos e sem ciclos.
- **Migração Strangler:** o código antigo virou fachada que delega ao módulo, com os testes existentes provando que o comportamento não mudou.

**DDD tático, sem cerimônia.**

- **Domínio puro e explícito:** ciclo de vida do projeto como máquina de estados (`queued → processing → completed | failed`), `Finding`/`Evidence`, `Rule` (uma regra por heurística) e `ScoringPolicy` como estratégia trocável da nota.
- **Funções em vez de classes** quando o estado vem do banco a cada request; a regra pura (`analysisStart`) é aplicada atomicamente por um `UPDATE ... WHERE` com a mesma condição.
- **Camada anticorrupção para o Stripe:** os tipos do Stripe param em `infrastructure/stripe/translate.ts`; a regra de direito ao plano (`entitlementFor`) é domínio puro.
- **`DomainError`** separa erro de negócio (mensagem para o usuário) de erro técnico (mensagem genérica).
- **Linguagem ubíqua** documentada em glossário e decisões em 11 ADRs.

**Consistência e execução assíncrona.**

- **Cota transacional (`withQuota`):** lock da linha do usuário → checagem do plano → trabalho → registro de uso, tudo ou nada; falha do sistema devolve a cota.
- **Vercel Workflows:** cada etapa da análise é um step com retry, só ids trafegam entre steps, erro do usuário é `FatalError` e um cron (reaper) encerra runs que morreram.

**Estratégia de testes (~730 testes + evals).**

| Nível | Como |
| :--- | :--- |
| Unidade (domínio) | Regras puras testadas sem mocks nem banco |
| Use cases | Fakes das portas (embedder e vector store em memória), sem baixar o modelo |
| Caracterização | Snapshot da saída completa da análise antes de refatorar as heurísticas |
| Componentes | Testing Library + jsdom |
| Integração | Postgres + pgvector reais com as migrations de verdade; recusa hosts remotos; testes de IDOR/isolamento entre usuários e de concorrência (validado por mutação) |
| E2E | Playwright na build de produção, com LLM fake determinístico |
| Evals de IA | Recall contra vulnerabilidades anotadas (NodeGoat, Juice Shop), evidência válida, resistência a prompt injection e falsos positivos proibidos; versões do prompt travadas em `prompts.lock.json` |

**CI obrigatório para merge:** lint, regras de arquitetura, migrations em sincronia com o schema, typecheck, testes, build, limite de tamanho de função, eval com quality gate, integração, E2E e OSV-Scanner.

**Engenharia como prática:** ADRs com alternativas e consequências, registro de dívida técnica (TD-xx), runbooks, postmortems, retrospectivas e baseline medido antes de otimizar.

</details>

---

## Architecture & Systems

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[Switchboard](https://github.com/renatomf/nextjs-switchboard)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 16` · `React Flow` · `Liveblocks` · `Trigger.dev` · `Browserbase` · `Stagehand` · `Neon` · `Drizzle` · `Clerk` · `Sentry` | Event-Driven + Realtime + Background Workers | Durable execution · workflow orchestration · distributed state · session lifecycle · observability | Plataforma visual para criar e executar automações de browser em sessões reais na nuvem, com colaboração em tempo real, execução durável e replay das sessões. |
| **[Nodebase](https://github.com/renatomf/nextjs-nodebase)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 16` · `React Flow` · `tRPC` · `Inngest` · `Prisma` · `PostgreSQL` · `Vercel AI SDK` · `Better Auth` · `Polar` · `Sentry` | Workflow Engine + Durable Execution | Workflow orchestration · retries · async processing · execution state · integrations | Editor visual de workflows que combina triggers, AI nodes e integrações externas com execução persistente em background e histórico das execuções. |
| **[Resonance](https://github.com/renatomf/nextjs-resonance)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 16` · `tRPC` · `Prisma` · `PostgreSQL` · `Cloudflare R2` · `Polar` · `Clerk` · `Chatterbox TTS` · `Sentry` | Multi-Tenant SaaS + Type-Safe API | Usage metering · object storage · presigned URLs · subscriptions · external services | Plataforma de geração de voz e voice cloning com processamento externo, armazenamento de áudio em object storage e billing baseado em uso. |
| **[TeamFlow](https://github.com/renatomf/nextjs-teamflow)**<br><sub>⭐⭐⭐</sub> | `Next.js 16` · `oRPC` · `TanStack Query` · `Prisma` · `PostgreSQL` · `Kinde` · `Arcjet` · `UploadThing` · `Tiptap` | Multi-Tenant SaaS + Type-Safe RPC | Tenant isolation · authorization · optimistic UI · rate limiting · abuse protection | Plataforma de colaboração com workspaces, canais, threads, mensagens, uploads e controle de acesso por organização. |

---

## Realtime & Collaboration

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[Docly](https://github.com/renatomf/nextjs-docly)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 15` · `Tiptap` · `Liveblocks` · `Yjs` · `Convex` · `Clerk` · `Zustand` | Realtime Collaboration + CRDT | Concurrent editing · conflict resolution · state synchronization · presence · permissions | Editor colaborativo de documentos com edição simultânea, cursores, presença, comentários, notificações e sincronização baseada em CRDT. |
| **[Flux](https://github.com/renatomf/nextjs-flux)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js` · `Socket.io` · `LiveKit` · `Clerk` · `Prisma` · `PostgreSQL` · `UploadThing` · `TanStack Query` | Realtime Messaging + WebRTC | WebSockets · WebRTC · presence · connection resilience · media delivery | Plataforma de comunicação com servidores, canais de texto, áudio e vídeo, mensagens realtime e fallback de WebSocket para polling. |
| **[Streamly](https://github.com/renatomf/nextjs-streamly)**<br><sub>⭐</sub> | `Next.js 14` · `Clerk` · `Tailwind CSS` · `Radix UI` · `shadcn/ui` · `next-themes` | Realtime Streaming Platform — Planned | Low-latency streaming · realtime chat · moderation · stream lifecycle | Plataforma de live streaming com transmissão de baixa latência, chat em tempo real, gerenciamento de stream keys e painel de controle do criador. |
| **[Sketchpad](https://github.com/renatomf/nextjs-sketchpad)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 14` · `Liveblocks` · `Convex` · `Clerk` · `perfect-freehand` · `Zustand` | Collaborative Canvas + Realtime State | Shared state · synchronization · presence · undo/redo · multi-user interaction | Whiteboard colaborativo com desenho livre, shapes, sticky notes, texto, camadas, cursores e histórico compartilhado. |
| **[Blip](https://github.com/renatomf/nextjs-blip)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `NextAuth.js` · `Prisma` · `Pusher` · `Cloudinary` · `Zustand` · `Axios` | API-Driven + Realtime Messaging | Realtime delivery · presence · read receipts · private channels · media | Aplicação de chat 1:1 e em grupo com mensagens em tempo real, presença online, confirmação de leitura e compartilhamento de imagens. |

---

## Workflows & Integrations

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[SendKit](https://github.com/renatomf/sendkit)**<br><sub>⭐⭐⭐⭐</sub> | `TypeScript` · `Bun` · `Hono` · `Zod` · `MCP` · `Clerk` · `Telegram Bot API` | Shared Core + Multi-Surface Architecture | Protocol abstraction · shared business logic · MCP · CLI · interface decoupling | Monorepo que expõe uma mesma operação de envio para Telegram através de CLI, MCP local e MCP remoto, centralizando a lógica em um único core. |
| **[VidFlow](https://github.com/renatomf/nextjs-vidflow)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 15` · `tRPC` · `Drizzle` · `Neon` · `Mux` · `UploadThing` · `Upstash Workflow` · `Svix` · `Clerk` | Async Media Pipeline + Webhook-Driven Architecture | Media processing · async workflows · webhooks · direct upload · rate limiting | Plataforma de vídeo com upload direto, processamento/transcodificação, streaming adaptativo, automações por IA e creator studio. |
| **[Signalist](https://github.com/renatomf/nextjs-signalist)**<br><sub>⭐⭐⭐</sub> | `Next.js 15` · `MongoDB` · `Mongoose` · `Better Auth` · `Inngest` · `Nodemailer` | Event-Driven Workflows + External Data Integration | External APIs · scheduled processing · alerts · notifications · background jobs | Plataforma de acompanhamento de mercado com watchlists, alertas de preço, insights financeiros, workflows e digests por e-mail. |
| **[Skillup](https://github.com/renatomf/nextjs-skillup)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `Prisma` · `PostgreSQL` · `Clerk` · `Mux` · `UploadThing` · `Stripe` · `Server Actions` | Server Actions + Webhook-Driven Integrations | Payment flows · media processing · webhooks · content lifecycle · progress tracking | Plataforma de cursos com criação e publicação de conteúdo, vídeo, anexos, pagamentos, progresso e dashboard administrativo. |

---

## Full Stack & SaaS

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[MediMeet](https://github.com/renatomf/nextjs-medimeet)**<br><sub>⭐</sub> | `Next.js 15` · `Prisma` · `Clerk` · `React Hook Form` · `Zod` · `Tailwind CSS` · `Radix UI` | Modular Full Stack + Role-Based Domain | Role-based access · domain modeling · scheduling · extensibility · secure workflows | Plataforma de atendimento remoto com separação entre fluxos de pacientes e médicos, agendamento de consultas e integração de pagamentos. |
| **[Marketto](https://github.com/renatomf/nextjs-marketto)**<br><sub>⭐⭐⭐</sub> | `Next.js 15` · `Sanity` · `Prisma` · `Oslo` · `Zustand` · `Zod` · `Tailwind CSS` | Headless Commerce + Custom Authentication | Content/data separation · session management · transactional persistence · client state | E-commerce com catálogo gerenciado por headless CMS, carrinho, autenticação própria e persistência transacional de usuários e pedidos. |
| **[Taskify](https://github.com/renatomf/nextjs-taskify)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `Prisma` · `Clerk` · `Stripe` · `TanStack Query` · `Zustand` · `DnD` | Multi-Tenant SaaS + Server Actions | Tenant isolation · authorization · optimistic interactions · audit logging · billing | SaaS de gestão de projetos com organizações, boards, listas, cards, drag-and-drop, audit log e planos de assinatura. |
| **[Lingo](https://github.com/renatomf/nextjs-lingo)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `Drizzle` · `Neon` · `Clerk` · `Stripe` · `React Admin` · `ElevenLabs` · `Zustand` | Server Actions + Gamified Domain Model | Domain state · progression systems · subscriptions · transactional updates | Plataforma de aprendizado gamificado com cursos, lições, XP, corações, leaderboard, quests, loja e plano Pro. |
| **[Beatstream](https://github.com/renatomf/nextjs-beatstream)**<br><sub>⭐⭐</sub> | `Next.js 14` · `Supabase` · `Stripe` · `Supabase Storage` · `Zustand` · `React Hook Form` | BaaS + Subscription Architecture | Media storage · persistent player state · subscriptions · webhook synchronization | Plataforma de streaming musical com upload de faixas, biblioteca pessoal, player persistente, favoritos e assinatura Premium sincronizada por webhook. |
| **[Havn](https://github.com/renatomf/nextjs-havn)**<br><sub>⭐⭐</sub> | `Next.js 13` · `NextAuth.js` · `Prisma` · `Leaflet` · `Cloudinary` · `SWR` · `Axios` | API-Driven Marketplace | Search · availability · reservation flows · geolocation · media management | Marketplace de acomodações com criação de anúncios, mapas, filtros, disponibilidade, reservas, favoritos e área de anfitrião. |

---

## Architecture & Engineering Patterns

- `Event-Driven Architecture`
- `Realtime Systems`
- `Distributed State`
- `Durable Execution`
- `Workflow Orchestration`
- `Multi-Tenancy`
- `AI Agents`
- `RAG`
- `Tool Calling`
- `CRDT`
- `WebRTC`
- `MCP`
- `Async Processing`
- `Webhook-Driven Integrations`
- `Object Storage`
- `Type-Safe APIs`
- `Sandboxed Execution`
- `Observability`
- `Modular Monolith`
- `Clean Architecture`
- `Domain-Driven Design`
- `Ports & Adapters`
- `Architecture Testing`
- `LLM Evals`
