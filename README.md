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
| **[Echo](https://github.com/renatomf/nextjs-echo)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 15` · `Turborepo` · `Convex` · `Clerk` · `Vapi` · `Jotai` · `Zod` · `Sentry` | Modular Monorepo + Realtime AI | AI agents · RAG · tool calling · multi-tenancy · human handoff | Plataforma de atendimento com widget embeddable, dashboard multi-tenant e agente de IA que resolve ou escala conversas para atendimento humano, com base de conhecimento por organização (RAG). |
| **[CraftAI](https://github.com/renatomf/nextjs-craftAI)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 15` · `tRPC` · `Prisma` · `Clerk` · `E2B` · `Inngest` · `Agent Kit` · `Zod` | Agentic Workflow + Isolated Sandbox | Agent orchestration · runtime isolation · code generation · live preview · rate limiting | Gerador de aplicações por IA que transforma linguagem natural em código, executa o resultado em sandbox isolado e disponibiliza preview ao vivo. |
| **[Polaris](https://github.com/renatomf/nextjs-polaris)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 16` · `CodeMirror 6` · `Vercel AI SDK` · `Gemini` · `Convex` · `Inngest` · `Firecrawl` · `Clerk` · `Sentry` | AI IDE + Reactive Backend + Async Jobs | Agent orchestration · code editing · background processing · web context · reactive state | IDE web com editor de código, árvore de arquivos reativa e jobs assíncronos para aquisição de contexto externo com IA, mantendo operações interativas separadas do processamento em background. |
| **[PlayForge](https://github.com/renatomf/nextjs-playforge)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 16` · `React 19` · `Vercel AI SDK` · `Trigger.dev` · `Drizzle` · `Neon` · `Clerk` · `Sentry` · `Tailwind CSS` | Durable AI Agent + Isolated Sandboxes + Multi-Provider LLM | Agent tools · human-in-the-loop · durable execution · usage ledger · multi-tenancy · observability | Construtor agêntico de jogos 3D: o agente faz perguntas, escreve o jogo num sandbox isolado na nuvem e entrega um preview jogável ao lado do chat, com créditos medidos por passo do modelo. |
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

<details>
<summary><b>Echo — arquitetura em detalhe</b></summary>

<br>

**Monorepo Turborepo** com dois apps e pacotes compartilhados: `apps/web` (dashboard do operador), `apps/widget` (widget embeddable), `packages/backend` (Convex), `packages/ui` (design system compartilhado) e configs de ESLint/TypeScript.

- **Backend Convex dividido por fronteira de acesso:** `public/` (chamado pelo widget, autenticado por sessão de contato), `private/` (dashboard, exige identidade Clerk com `orgId`) e `system/` (funções internas e IA).
- **Multi-tenancy por organização:** conversas e sessões indexadas por `organizationId`; toda função privada recusa chamadas sem organização ativa.
- **Sessões de contato com expiração (24 h)**, validadas em cada chamada pública.
- **Agente de suporte** com `@convex-dev/agent` e tools `resolveConversation` / `escalateConversation`; a conversa segue `unresolved → escalated → resolved`, e o agente só responde enquanto ela não foi escalada para um humano (human handoff).
- **Base de conhecimento por organização** com `@convex-dev/rag` (um namespace por org, embeddings `text-embedding-3-small`): upload, listagem e remoção de arquivos pelo dashboard.
- Estado de UI com Jotai (telas do widget) e Sentry no app web.

</details>

<details>
<summary><b>CraftAI — arquitetura em detalhe</b></summary>

<br>

- **Módulos por feature** (`src/modules/<feature>/server/procedures.ts` + `ui/`), compostos num router tRPC único com `protectedProcedure` (Clerk).
- **Rede de agentes com Inngest Agent Kit:** `createNetwork` com router e limite de 15 iterações; o agente de código usa as tools `terminal`, `createOrUpdateFiles` e `readFiles`, que atuam dentro de um sandbox E2B.
- **Sandbox isolado** a partir de um template próprio (`sandbox-templates/nextjs`); o resultado vira um *fragment* com a URL do preview e os arquivos gerados.
- **Agentes de pós-processamento** geram o título do fragment e a resposta final ao usuário.
- **Execução assíncrona:** a mutation só enfileira o evento; o job roda no Inngest e a UI acompanha pelo React Query.
- **Créditos por plano** com `rate-limiter-flexible` persistido no Prisma (janela de 30 dias, limite diferente para free e pro via Clerk Billing).

</details>

<details>
<summary><b>Polaris — arquitetura em detalhe</b></summary>

<br>

- **Organização por feature** (`src/features/{auth,editor,projects}`), cada uma com componentes, hooks, store e extensões.
- **Convex como backend reativo:** projetos e uma árvore de arquivos (`parentId`, índice `by_project_parent`), com storage para binários; toda função valida identidade (Clerk) e dono do projeto.
- **Optimistic updates** na criação e renomeação de projetos (`withOptimisticUpdate`).
- **Editor CodeMirror 6 com extensões próprias:** linguagem por extensão de arquivo, minimap, indentation markers e tema; abas por projeto (preview e fixadas, no estilo VS Code) em Zustand.
- **Jobs assíncronos com Inngest** (middleware do Sentry): pipeline em steps que extrai URLs, faz scraping com Firecrawl e gera texto com Gemini.
- **Em andamento:** agente de IA integrado ao editor e importação/exportação de projetos (já modeladas no schema).

</details>

<details>
<summary><b>PlayForge — arquitetura em detalhe</b></summary>

<br>

- **Agente durável:** o chat roda como `chat.agent` do Trigger.dev, desacoplado da request HTTP; fechar a aba não mata a resposta e recarregar a página retoma o stream.
- **Um jogo = um chat = um sandbox Daytona**, com sistema de arquivos, processo e porta próprios; o código gerado nunca roda no servidor da aplicação.
- **Tools tipadas com Zod** (`write_file`, `replace_text`, `read_file`, `list_files`, `delete_file`) e `ask_player`, que pede ao usuário respostas de múltipla escolha antes de construir (human-in-the-loop).
- **Catálogo multi-provider** (Anthropic, Google, Groq, Alibaba e Ollama local) dividido em parte segura para o client e parte server-only; `satisfies Record<GameModelId, …>` transforma um modelo esquecido em erro de compilação.
- **Créditos num ledger append-only:** cada passo do modelo é cobrado com o id da resposta como chave (idempotente) e conciliado com o plano do Clerk Billing.
- **Multi-tenancy por organização (Clerk Organizations)** em toda consulta; o worker só age em jogos cujo acesso já foi checado no servidor.
- **Observabilidade ponta a ponta** com Sentry no browser, servidor, edge e worker; engine 3D própria sobre Three.js.

</details>

<details>
<summary><b>Meet AI — arquitetura em detalhe</b></summary>

<br>

- **Módulos por feature** (`agents`, `meetings`, `call`, `auth`), cada um com `server/procedures.ts`, schemas Zod e filtros na URL (nuqs); tRPC + React Query com prefetch e hidratação no servidor.
- **Drizzle + Neon**, com status de reunião como enum no Postgres e Better Auth para autenticação.
- **Orquestração por webhooks do Stream** (assinatura verificada): ao iniciar a chamada, conecta um agente OpenAI Realtime com as instruções do agente; quando um participante sai, a chamada é encerrada; com a transcrição pronta, dispara um job.
- **Resumo assíncrono com Inngest:** steps para buscar a transcrição, parsear o JSONL, identificar os falantes, resumir com um agente e salvar.
- **Chat pós-reunião:** mensagens no Stream Chat são respondidas pelo agente com o resumo da reunião como contexto.

</details>

---

## Architecture & Systems

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[Switchboard](https://github.com/renatomf/nextjs-switchboard)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 16` · `React Flow` · `Liveblocks` · `Trigger.dev` · `Browserbase` · `Stagehand` · `Neon` · `Drizzle` · `Clerk` · `Sentry` | Event-Driven + Realtime + Background Workers | Durable execution · workflow orchestration · distributed state · session lifecycle · observability | Plataforma visual para criar e executar automações de browser em sessões reais na nuvem, com colaboração em tempo real, execução durável e replay das sessões. |
| **[Nodebase](https://github.com/renatomf/nextjs-nodebase)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 16` · `React Flow` · `tRPC` · `Inngest` · `Prisma` · `PostgreSQL` · `Vercel AI SDK` · `Better Auth` · `Polar` · `Sentry` | Workflow Engine + Durable Execution | Workflow orchestration · retries · async processing · execution state · integrations | Editor visual de workflows que combina triggers, AI nodes e integrações externas com execução persistente em background e histórico das execuções. |
| **[Resonance](https://github.com/renatomf/nextjs-resonance)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 16` · `tRPC` · `Prisma` · `PostgreSQL` · `Cloudflare R2` · `Polar` · `Clerk` · `Chatterbox TTS` · `Sentry` | Multi-Tenant SaaS + Type-Safe API | Usage metering · object storage · presigned URLs · subscriptions · external services | Plataforma de geração de voz e voice cloning com processamento externo, armazenamento de áudio em object storage e billing baseado em uso. |
| **[TeamFlow](https://github.com/renatomf/nextjs-teamflow)**<br><sub>⭐⭐⭐</sub> | `Next.js 16` · `oRPC` · `TanStack Query` · `Prisma` · `PostgreSQL` · `Kinde` · `Arcjet` · `UploadThing` · `Tiptap` | Multi-Tenant SaaS + Type-Safe RPC | Tenant isolation · authorization · optimistic UI · rate limiting · abuse protection | Plataforma de colaboração com workspaces, canais, threads, mensagens, uploads e controle de acesso por organização. |

<details>
<summary><b>Switchboard — arquitetura em detalhe</b></summary>

<br>

- **Feature `workflows`** com fronteiras claras: `components/` (canvas), `engine/` (motor), `lib/` (regras puras, sem I/O, com o teste ao lado), `nodes/` (um arquivo por nó), `tasks/` (Trigger.dev) e `data.ts`, a única camada que conhece o Drizzle.
- **Versões imutáveis:** o rascunho vive no Liveblocks; no Run, o grafo é congelado no Postgres e a task executa a versão, não o rascunho.
- **Motor `runSteps` atrás de portas** (`BrowserPort`, `ProgressReporter`, `RunLogger`), percorrendo os nós em ordem topológica e testado com dublês.
- **Execuções como máquina de estados** com transições protegidas no próprio `upsert`, fila com concorrência por organização, no máximo uma run viva por workflow (trava no Postgres) e uma varredura que concilia execuções travadas.
- **Registry de nós com `satisfies`:** um executor faltando vira erro de compilação.
- **Segurança:** webhooks com HMAC no formato do Stripe (janela de 5 min, comparação em tempo constante), cofre de credenciais com envelope encryption e redação de segredos antes de gravar erros.
- **Qualidade:** cerca de 40 arquivos de teste Vitest (TDD nas regras puras), E2E com Playwright e login programático do Clerk no preview de cada PR; o CI exige lint, formatação, código morto (knip), typecheck, testes, build, migrations em sincronia e auditoria de dependências. 13 ADRs e postmortem.

</details>

<details>
<summary><b>Nodebase — arquitetura em detalhe</b></summary>

<br>

- **Organização por feature** (`workflows`, `executions`, `credentials`, `triggers`, `editor`, `subscriptions`), cada uma com `server/routers.ts`, prefetch, params na URL (nuqs), hooks e componentes.
- **Nós como plugins:** cada nó tem sua pasta (`executor.ts`, `node.tsx`, `dialog.tsx`, `actions.ts`) e entra num `executor-registry` (Strategy); um nó novo não exige mudar o motor.
- **Motor de execução no Inngest:** cria o registro da execução, ordena o grafo com ordenação topológica (`toposort`), executa os nós em sequência passando um contexto e grava o status final.
- **Variáveis entre nós** com templates Handlebars; status de cada nó transmitido em tempo real por canais do `@inngest/realtime`.
- **Triggers** manual, Google Forms e Stripe (webhooks).
- **Credenciais criptografadas em repouso** (Cryptr); procedures `protected` e `premium` no tRPC, com assinatura Polar via plugin do Better Auth.

</details>

<details>
<summary><b>Resonance — arquitetura em detalhe</b></summary>

<br>

- **Organização por feature** (`voices`, `text-to-speech`, `billing`, `dashboard`) e routers tRPC com procedures encadeadas: base (middleware do Sentry) → autenticada → organização (exige `orgId` do Clerk).
- **Multi-tenancy** com `orgId` indexado em vozes e gerações; vozes do sistema (sem org) entram por script de seed.
- **Serviço de TTS externo** (Chatterbox) consumido por um client tipado gerado a partir do OpenAPI (`scripts/sync-api.ts` + `openapi-fetch`).
- **Áudio no Cloudflare R2** via API S3, entregue por URL pré-assinada de 1 h.
- **Billing por uso com Polar:** checkout, portal do cliente e um evento de medição por geração (uma falha na medição não quebra a experiência).
- **Variáveis de ambiente validadas** com `@t3-oss/env-nextjs`; formulários com TanStack Form.

</details>

<details>
<summary><b>TeamFlow — arquitetura em detalhe</b></summary>

<br>

- **API oRPC** (`workspace`, `channel`, `message`, `member`) com middlewares compostos por rota: autenticação → workspace ativo → Arcjet (shield + detecção de bots) → rate limit por perfil de operação (leitura, escrita, escrita pesada) com janela deslizante.
- **Workspaces como organizações do Kinde**, com a Management API para membros.
- **Schemas Zod compartilhados** em `app/schemas` entre cliente e servidor.
- **TanStack Query com hidratação SSR** e serializer próprio; reações com optimistic update e rollback na lista e na thread.
- **Rich text com Tiptap** salvo como JSON e sanitizado com DOMPurify antes de renderizar; uploads com UploadThing.

</details>

---

## Realtime & Collaboration

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[Docly](https://github.com/renatomf/nextjs-docly)**<br><sub>⭐⭐⭐⭐⭐</sub> | `Next.js 15` · `Tiptap` · `Liveblocks` · `Yjs` · `Convex` · `Clerk` · `Zustand` | Realtime Collaboration + CRDT | Concurrent editing · CRDT · presence · anchored comments · permissions | Editor colaborativo de documentos com edição simultânea, cursores, presença, comentários ancorados ao texto, busca e permissões por dono ou organização. |
| **[Flux](https://github.com/renatomf/nextjs-flux)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js` · `Socket.io` · `LiveKit` · `Clerk` · `Prisma` · `PostgreSQL` · `UploadThing` · `TanStack Query` | Realtime Messaging + WebRTC | WebSockets · WebRTC · presence · connection resilience · media delivery | Plataforma de comunicação com servidores, canais de texto, áudio e vídeo, mensagens realtime e fallback de WebSocket para polling. |
| **[Streamly](https://github.com/renatomf/nextjs-streamly)**<br><sub>⭐</sub> | `Next.js 14` · `Clerk` · `Tailwind CSS` · `Radix UI` · `shadcn/ui` · `next-themes` | Realtime Streaming Platform — Planned | Low-latency streaming · realtime chat · moderation · stream lifecycle | Plataforma de live streaming com transmissão de baixa latência, chat em tempo real, gerenciamento de stream keys e painel de controle do criador. |
| **[Sketchpad](https://github.com/renatomf/nextjs-sketchpad)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 14` · `Liveblocks` · `Convex` · `Clerk` · `perfect-freehand` · `Zustand` | Collaborative Canvas + Realtime State | Shared state · synchronization · presence · undo/redo · multi-user interaction | Whiteboard colaborativo com desenho livre, shapes, sticky notes, texto, camadas, cursores e histórico compartilhado. |
| **[Blip](https://github.com/renatomf/nextjs-blip)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `NextAuth.js` · `Prisma` · `Pusher` · `Cloudinary` · `Zustand` · `Axios` | API-Driven + Realtime Messaging | Realtime delivery · presence · read receipts · event channels · media | Aplicação de chat 1:1 e em grupo com mensagens em tempo real, presença online, confirmação de leitura e compartilhamento de imagens. |

<details>
<summary><b>Docly — arquitetura em detalhe</b></summary>

<br>

- **Convex** para documentos, com `ownerId` e `organizationId` e índice de busca full-text no título, filtrado por organização ou dono; editar e excluir só para o dono ou membros da organização.
- **Edição colaborativa** com Tiptap + Liveblocks (CRDT Yjs por baixo): uma sala por documento, cursores e presença.
- **Comentários ancorados ao texto** (threads flutuantes e ancoradas do Liveblocks).
- **Extensões próprias do Tiptap** (tamanho de fonte, altura de linha) e a instância do editor compartilhada com a toolbar via Zustand; busca na URL com nuqs.

</details>

<details>
<summary><b>Flux — arquitetura em detalhe</b></summary>

<br>

- **App Router + Pages API:** route handlers REST para servidores, canais e membros; o servidor Socket.io fica na Pages API, que dá acesso ao servidor HTTP persistente.
- **Escrita → persistência → evento:** a mensagem é gravada com Prisma e emitida numa chave por canal (`chat:<id>:messages`) e numa chave de atualização para edição e exclusão.
- **Cliente resiliente:** infinite query com paginação por cursor (lotes de 10); o hook do socket atualiza o cache do React Query e, sem conexão, cai para polling de 1 s.
- **Papéis** `ADMIN` / `MODERATOR` / `GUEST` por membro e canais `TEXT` / `AUDIO` / `VIDEO`; token do LiveKit gerado no servidor para áudio e vídeo.
- Estado dos modais em Zustand; uploads com UploadThing.

</details>

<details>
<summary><b>Streamly — arquitetura em detalhe</b></summary>

<br>

- **Estágio atual:** autenticação com Clerk e a base de layout (App Router, Radix UI, shadcn/ui, tema claro e escuro).
- **Próximos passos:** ingestão e transmissão de baixa latência, chat em tempo real e gerenciamento de stream keys.

</details>

<details>
<summary><b>Sketchpad — arquitetura em detalhe</b></summary>

<br>

- **Convex** para boards e favoritos por usuário, com consultas filtradas pela organização ativa (Clerk).
- **Autorização da sala no servidor:** a rota de auth do Liveblocks confere se o board pertence à organização do usuário antes de liberar acesso.
- **Estado compartilhado modelado com CRDTs do Liveblocks:** `LiveMap` de camadas + `LiveList` com a ordem; mutations com `useMutation` e undo/redo com `useHistory` (pausado durante o arraste).
- **Canvas como máquina de estados** (`CanvasMode`: seleção, translação, inserção, redimensionamento, lápis…), com traço suave via perfect-freehand.

</details>

<details>
<summary><b>Blip — arquitetura em detalhe</b></summary>

<br>

- **Route handlers REST** para conversas, mensagens e configurações, e loaders server-side em `app/actions`; NextAuth com credenciais (bcrypt) e OAuth, via Prisma adapter.
- **Pusher por canal:** eventos no canal da conversa (`messages:new`, `message:update`) e no canal do usuário (`conversation:new` / `update` / `remove`).
- **Presença online** num presence channel autenticado pela rota `/api/pusher/auth`, com a lista de ativos em Zustand.
- **Confirmação de leitura** com relação N:N de quem viu cada mensagem; imagens enviadas pelo Cloudinary.

</details>

---

## Workflows & Integrations

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[SendKit](https://github.com/renatomf/sendkit)**<br><sub>⭐⭐⭐⭐</sub> | `TypeScript` · `Bun` · `Hono` · `Zod` · `MCP` · `Clerk` · `Telegram Bot API` | Shared Core + Multi-Surface Architecture | Protocol abstraction · shared business logic · MCP · CLI · interface decoupling | Monorepo que expõe uma mesma operação de envio para Telegram através de CLI, MCP local e MCP remoto, centralizando a lógica em um único core. |
| **[VidFlow](https://github.com/renatomf/nextjs-vidflow)**<br><sub>⭐⭐⭐⭐</sub> | `Next.js 15` · `tRPC` · `Drizzle` · `Neon` · `Mux` · `UploadThing` · `Upstash Workflow` · `Svix` · `Clerk` | Async Media Pipeline + Webhook-Driven Architecture | Media processing · async workflows · webhooks · direct upload · rate limiting | Plataforma de vídeo com upload direto, processamento/transcodificação via webhooks, streaming adaptativo, comentários, playlists, inscrições e creator studio. |
| **[Signalist](https://github.com/renatomf/nextjs-signalist)**<br><sub>⭐⭐⭐</sub> | `Next.js 15` · `MongoDB` · `Mongoose` · `Better Auth` · `Inngest` · `Nodemailer` | Event-Driven Workflows + External Data Integration | External APIs · scheduled processing · alerts · notifications · background jobs | Plataforma de acompanhamento de mercado com watchlists, dados de ações, e-mail de boas-vindas personalizado por IA e digest diário de notícias. |
| **[Skillup](https://github.com/renatomf/nextjs-skillup)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `Prisma` · `PostgreSQL` · `Clerk` · `Mux` · `UploadThing` · `Stripe` · `Server Actions` | Server Actions + Webhook-Driven Integrations | Payment flows · media processing · webhooks · content lifecycle · progress tracking | Plataforma de cursos com criação e publicação de conteúdo, vídeo, anexos, pagamentos, progresso e dashboard administrativo. |

<details>
<summary><b>SendKit — arquitetura em detalhe</b></summary>

<br>

- **Monorepo Bun** com um core e três superfícies: `packages/core` (operação + schemas Zod), `packages/cli` (Commander, publicado no npm), `packages/local-mcp` (MCP via stdio) e `apps/remote-mcp` (Hono, MCP por Streamable HTTP).
- **Uma operação, várias interfaces:** CLI, MCP local e MCP remoto chamam a mesma `sendTelegramMessage`, sem duplicar regra.
- **Validação nas duas pontas:** entrada, request ao Telegram e resposta da API passam por schemas Zod.
- **MCP remoto protegido por OAuth:** metadata de recurso protegido gerada com `@clerk/mcp-tools`.
- Build com tsdown e uma skill para agentes usarem a ferramenta.

</details>

<details>
<summary><b>VidFlow — arquitetura em detalhe</b></summary>

<br>

- **Módulos por feature** (`videos`, `studio`, `comments`, `playlists`, `subscriptions`, reações, views, busca, sugestões), cada um com `server/procedures.ts` composto no tRPC.
- **Drizzle + drizzle-zod:** schemas de validação derivados das tabelas.
- **Pipeline de mídia dirigido por webhooks:** upload direto para o Mux; o webhook (assinatura verificada) atualiza o ciclo de vida do asset (criado → pronto com playback id, thumbnail e duração → erro / removido) e as legendas. Usuários do Clerk sincronizados via Svix.
- **Rate limit por usuário** (Upstash Ratelimit) no `protectedProcedure`.
- **Paginação por cursor** nos feeds infinitos; Upstash Workflow para jobs em background.

</details>

<details>
<summary><b>Signalist — arquitetura em detalhe</b></summary>

<br>

- **Server actions por domínio** (`auth`, `watchlist`, `finnhub`) com modelos Mongoose; Better Auth com MongoDB e middleware protegendo as rotas.
- **Dados de mercado (Finnhub)** com cache do `fetch` (revalidate) e `cache` do React.
- **Workflows com Inngest:** no cadastro, `step.ai.infer` (Gemini) gera uma introdução personalizada para o e-mail de boas-vindas; um cron diário busca notícias da watchlist de cada usuário, resume com IA e envia o digest por Nodemailer.

</details>

<details>
<summary><b>Skillup — arquitetura em detalhe</b></summary>

<br>

- **Route handlers REST por recurso** (cursos, capítulos, anexos, publicação, reordenação, progresso, checkout) e loaders server-side em `actions/`.
- **Vídeo com Mux:** ao trocar o vídeo de um capítulo, o asset anterior é removido e o novo `playbackId` é salvo.
- **Pagamento com Stripe Checkout** e webhook com assinatura verificada, que registra a compra em `checkout.session.completed`.
- **Regras de acesso:** capítulos gratuitos ou liberados pela compra; curso só publica com capítulo publicado; área do professor restrita.
- **Reordenação drag-and-drop** persistida por posição; progresso por usuário e analytics de receita por curso.

</details>

---

## Full Stack & SaaS

| Projeto | Stack | Arquitetura | Foco de Engenharia | Detalhes |
| :--- | :--- | :--- | :--- | :--- |
| **[MediMeet](https://github.com/renatomf/nextjs-medimeet)**<br><sub>⭐</sub> | `Next.js 15` · `Prisma` · `Clerk` · `React Hook Form` · `Zod` · `Tailwind CSS` · `Radix UI` | Modular Full Stack + Role-Based Domain | Role-based access · domain modeling · scheduling · extensibility · secure workflows | Plataforma de atendimento remoto com onboarding por papel (paciente e médico), verificação de médicos, créditos por plano e domínio modelado para agendamento e repasses. |
| **[Marketto](https://github.com/renatomf/nextjs-marketto)**<br><sub>⭐⭐⭐</sub> | `Next.js 15` · `Sanity` · `Prisma` · `Oslo` · `Zustand` · `Zod` · `Tailwind CSS` | Headless Commerce + Custom Authentication | Content/data separation · session management · transactional persistence · client state | E-commerce com catálogo gerenciado por headless CMS, carrinho sincronizado entre cliente e servidor e autenticação própria baseada em sessões. |
| **[Taskify](https://github.com/renatomf/nextjs-taskify)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `Prisma` · `Clerk` · `Stripe` · `TanStack Query` · `Zustand` · `DnD` | Multi-Tenant SaaS + Server Actions | Tenant isolation · authorization · optimistic interactions · audit logging · billing | SaaS de gestão de projetos com organizações, boards, listas, cards, drag-and-drop, audit log e planos de assinatura. |
| **[Lingo](https://github.com/renatomf/nextjs-lingo)**<br><sub>⭐⭐⭐</sub> | `Next.js 14` · `Drizzle` · `Neon` · `Clerk` · `Stripe` · `React Admin` · `ElevenLabs` · `Zustand` | Server Actions + Gamified Domain Model | Domain state · progression systems · subscriptions · admin panel | Plataforma de aprendizado gamificado com cursos, lições, XP, corações, leaderboard, quests, loja e plano Pro. |
| **[Beatstream](https://github.com/renatomf/nextjs-beatstream)**<br><sub>⭐⭐</sub> | `Next.js 14` · `Supabase` · `Stripe` · `Supabase Storage` · `Zustand` · `React Hook Form` | BaaS + Subscription Architecture | Media storage · persistent player state · subscriptions · webhook synchronization | Plataforma de streaming musical com upload de faixas, biblioteca pessoal, player persistente, favoritos e assinatura Premium sincronizada por webhook. |
| **[Havn](https://github.com/renatomf/nextjs-havn)**<br><sub>⭐⭐</sub> | `Next.js 13` · `NextAuth.js` · `Prisma` · `Leaflet` · `Cloudinary` · `SWR` · `Axios` | API-Driven Marketplace | Search · availability · reservation flows · geolocation · media management | Marketplace de acomodações com criação de anúncios, mapas, filtros, disponibilidade, reservas, favoritos e área de anfitrião. |

<details>
<summary><b>MediMeet — arquitetura em detalhe</b></summary>

<br>

- **Modelo de domínio rico no Prisma:** papel do usuário (`UNASSIGNED` / `PATIENT` / `DOCTOR` / `ADMIN`), verificação de médicos, slots de disponibilidade, consultas com status, transações de crédito e repasses.
- **Onboarding por papel** em server action com validação: pacientes entram direto, médicos enviam dados e ficam pendentes de verificação.
- **Créditos mensais por plano** (Clerk Billing), alocados uma vez por mês e plano.
- Usuário do Clerk sincronizado com o banco no primeiro acesso.
- **Em andamento:** fluxos de agendamento, consulta e repasse (já modelados no schema).

</details>

<details>
<summary><b>Marketto — arquitetura em detalhe</b></summary>

<br>

- **Separação conteúdo / dados:** catálogo, categorias e promoções no Sanity (Studio embutido em `/studio`); usuários, sessões e carrinhos no Postgres via Prisma.
- **Autenticação própria** com Oslo: token aleatório, só o hash SHA-256 guardado como id da sessão, expiração de 30 dias com renovação deslizante e cookie httpOnly.
- **Carrinho** em Zustand no cliente, sincronizado com o carrinho do servidor; o carrinho anônimo é mesclado ao do usuário no login.
- Server actions para autenticação e carrinho.

</details>

<details>
<summary><b>Taskify — arquitetura em detalhe</b></summary>

<br>

- **Server actions padronizadas:** uma pasta por ação (`schema.ts` Zod, `types.ts`, `index.ts`) envolvida por `createSafeAction`, com erros por campo tipados e um hook `useAction` no cliente.
- **Multi-tenancy com Clerk Organizations:** o `orgId` filtra todas as consultas.
- **Limite do plano free por organização** (contador) e assinatura Stripe por organização, sincronizada por webhook.
- **Audit log** em cada mutação (entidade, ação, autor).
- Drag-and-drop de listas e cards com atualização da ordem; React Query no modal de detalhes do card.

</details>

<details>
<summary><b>Lingo — arquitetura em detalhe</b></summary>

<br>

- **Domínio em Drizzle:** curso → unidade → lição → desafio → opções, mais progresso do usuário (corações, pontos, curso ativo), progresso por desafio e assinatura.
- **Consultas centralizadas** em `db/queries.ts` com `cache` do React; server actions para as regras de progresso (perda e recuperação de corações, pontos, modo prática) com `revalidatePath`.
- **Plano Pro via Stripe** (corações ilimitados), sincronizado por webhook.
- **Painel admin com React Admin** sobre rotas REST protegidas por allowlist; scripts de seed.

</details>

<details>
<summary><b>Beatstream — arquitetura em detalhe</b></summary>

<br>

- **Supabase como BaaS:** autenticação, Postgres e Storage para faixas e capas.
- **Loaders server-side** em `actions/` com o client do Supabase no servidor; hooks no cliente para player e uploads.
- **Player persistente** com estado em Zustand, montado no layout para não parar na navegação.
- **Assinatura sincronizada por webhook:** produtos, preços e assinaturas do Stripe replicados no Supabase com o client admin.

</details>

<details>
<summary><b>Havn — arquitetura em detalhe</b></summary>

<br>

- **Route handlers REST** (anúncios, reservas, favoritos, cadastro) e loaders server-side em `app/actions` com Prisma; NextAuth com credenciais e OAuth.
- **Disponibilidade na consulta:** a busca exclui anúncios com reserva sobreposta ao período pedido e filtra por hóspedes, quartos, banheiros e localização vindos da query string.
- **Reservas com papéis:** o hóspede gerencia as próprias viagens e o anfitrião, as reservas das suas propriedades.
- Modais com estado em Zustand, mapa com Leaflet e uploads pelo Cloudinary.

</details>

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
