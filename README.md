<div align="center">

# Olá, eu sou o Renato 👋

Desenvolvedor front-end & full-stack com foco em Next.js, React, TypeScript, arquitetura de sistemas e produtos com IA.

[![GitHub followers](https://img.shields.io/github/followers/renatomf?style=social)](https://github.com/renatomf)

</div>

---

## Repositórios por profundidade técnica

Organizados em seis níveis, do sistema mais sofisticado ao mais recente em desenvolvimento.

---

### Nível I — Arquitetura de Sistemas
*Infraestrutura construída, não apenas consumida de uma plataforma pronta.*

| # | Projeto | Stack | Destaque arquitetural |
|---|---|---|---|
| 1 | [Nodebase](https://github.com/renatomf/nextjs-nodebase) | Next.js 16 · React Flow · tRPC · Inngest · Prisma · PostgreSQL | Motor de automação de workflows: um canvas de nós conecta gatilhos, IA e integrações num fluxo executável, processado por um motor de execução durável — com retomada automática, histórico completo e credenciais criptografadas. A aplicação constrói a própria infraestrutura de orquestração, em vez de delegar isso a uma plataforma pronta |
| 2 | [Echo](https://github.com/renatomf/nextjs-echo) | Next.js 15 · Turborepo · Convex · Clerk · Vapi | Plataforma de atendimento com IA: duas aplicações (widget + painel) compartilham um backend reativo em tempo real; o agente responde com base em busca semântica sobre uma base de conhecimento própria, aciona ferramentas externas e escala para um humano quando necessário |
| 3 | [Resonance](https://github.com/renatomf/nextjs-resonance) | Next.js 16 · tRPC · Prisma · Cloudflare R2 · Polar | SaaS multi-tenant de texto-para-voz com clonagem de voz; uploads e downloads de áudio acontecem via URLs pré-assinadas direto no storage (sem proxy pelo servidor), billing por uso e cliente de API auto-gerado a partir de um spec OpenAPI externo |
| 4 | [Meet AI](https://github.com/renatomf/nextjs-meet-ai) | Next.js 15 · Drizzle ORM · Neon · Stream Video · OpenAI Realtime · Inngest | Agentes de IA que participam de videochamadas ao vivo, respondendo por voz em tempo real; a resposta síncrona (baixa latência) é isolada do resumo pós-reunião, processado de forma assíncrona — uma separação deliberada entre o que precisa responder na hora e o que pode esperar |
| 5 | [CraftAI](https://github.com/renatomf/nextjs-craftAI) | Next.js 15 · tRPC · Prisma · E2B · Inngest Agent Kit | Gerador de aplicativos por IA: o usuário descreve o app, um agente escreve e edita o código dentro de um sandbox isolado com servidor de desenvolvimento ao vivo, e o preview funcional aparece em tempo real ao lado do código gerado |
| 6 | [TeamFlow](https://github.com/renatomf/nextjs-teamflow) | Next.js 16 · oRPC · Prisma · Kinde · Arcjet · Tiptap | Plataforma de mensagens em equipe (estilo Slack) com segurança de API como cidadã de primeira classe: toda requisição passa por proteção contra bots e rate limiting (Arcjet) antes de tocar no banco, com updates otimistas via TanStack Query |
| 7 | [SendKit](https://github.com/renatomf/sendkit) | Bun · TypeScript · Hono · Zod · Model Context Protocol · Clerk | Uma única lógica de negócio (`sendTelegramMessage`) exposta por três superfícies — CLI, servidor MCP local (stdio) e servidor MCP remoto (HTTP com OAuth) — todas consumindo o mesmo pacote `core`, sem dependência de I/O além de `fetch` |

### Nível II — Complexidade de Domínio
*O produto resolve algo já conhecido no mercado, mas com um componente interno que exige domínio de um problema técnico específico e não trivial.*

| # | Projeto | Stack | Destaque arquitetural |
|---|---|---|---|
| 8 | [Docly](https://github.com/renatomf/nextjs-docly) | Next.js 15 · Liveblocks (Yjs/CRDT) · Tiptap · Convex · Clerk | Editor de documentos colaborativo (estilo Google Docs): múltiplas pessoas editam o mesmo texto simultaneamente, e o merge converge para o mesmo estado final independente da ordem de chegada dos eventos — sem lock, sem "última escrita vence" |
| 9 | [Sketchpad](https://github.com/renatomf/nextjs-sketchpad) | Next.js 14 · Liveblocks (Yjs/CRDT) · Convex · Clerk · perfect-freehand | Quadro branco colaborativo (estilo Miro) aplicando o mesmo princípio de CRDT a um grafo de objetos geométricos — formas, camadas, posição e seleção sincronizados em tempo real entre participantes, com undo/redo compartilhado |
| 10 | [VidFlow](https://github.com/renatomf/nextjs-vidflow) | Next.js 15 · Drizzle ORM · Neon · Clerk · Mux · Upstash Workflow · Svix | Plataforma de hospedagem de vídeo (estilo YouTube) com um pipeline assíncrono completo: upload → transcodificação externa → callback assinado → job em fila → geração de metadados por IA → publicação, orquestrando múltiplos sistemas externos com estados intermediários |
| 11 | [Temu Clone](https://github.com/renatomf/nextjs-temu-clone) | Next.js 15 · Sanity · Prisma · Oslo · Zustand | Marketplace de e-commerce (estilo Temu) com autenticação implementada do zero — hashing e tokens de sessão via primitivas de criptografia de baixo nível (padrão Lucia Auth), em vez de um provedor terceirizado, com catálogo gerenciado por CMS headless |

### Nível III — Orquestração & Integração
*Múltiplos serviços externos coordenados em conjunto, com dados fluindo entre etapas assíncronas.*

| # | Projeto | Stack | Destaque arquitetural |
|---|---|---|---|
| 12 | [Skillup](https://github.com/renatomf/nextjs-skillup) | Next.js 14 · Prisma · Clerk · Mux · Stripe | Plataforma de cursos online (estilo Udemy) com vídeo processado externamente, pagamento único por curso e um fluxo de publicação que valida requisitos mínimos (capa, descrição, categoria, ao menos um capítulo) antes de liberar o conteúdo |
| 13 | [Signalist](https://github.com/renatomf/nextjs-signalist) | Next.js 15 · MongoDB · Better Auth · Inngest · Nodemailer | Plataforma de acompanhamento de ações com jobs em background consultando uma API externa de mercado e disparando alertas personalizados por e-mail conforme condições definidas pelo usuário |
| 14 | [Flux](https://github.com/renatomf/nextjs-flux) | Next.js · Socket.io · LiveKit · Clerk · Prisma | Plataforma de comunicação (estilo Discord) com mensagens em tempo real e canais de voz/vídeo via WebRTC, com fallback automático de WebSocket para polling em redes instáveis |

### Nível IV — Produtos Full-Stack
*Casos de uso reais resolvidos ponta a ponta, com boas práticas de integração já estabelecidas na indústria.*

| # | Projeto | Stack | Destaque arquitetural |
|---|---|---|---|
| 15 | [Havn](https://github.com/renatomf/nextjs-havn) | Next.js 13 · NextAuth.js · Prisma · Leaflet · Cloudinary | Plataforma de aluguel de acomodações (estilo Airbnb) com busca georreferenciada em mapa interativo e verificação de conflito de disponibilidade antes de confirmar uma reserva |
| 16 | [Taskify](https://github.com/renatomf/nextjs-taskify) | Next.js 14 · Prisma · Clerk (organizações) · Stripe | Gestão de tarefas (estilo Trello) em quadros por workspace, com cards reordenáveis por arrastar-e-soltar, limite de uso por plano e upgrade via Stripe |
| 17 | [Lingo](https://github.com/renatomf/nextjs-lingo) | Next.js 14 · Drizzle ORM · Neon · Clerk · Stripe · React Admin | Plataforma gamificada de aprendizado de idiomas (estilo Duolingo) com sistema de pontos e vidas, ranking entre usuários e painel administrativo dedicado à gestão do conteúdo educacional |
| 18 | [Blip](https://github.com/renatomf/nextjs-blip) | Next.js 14 · NextAuth.js · Prisma · Pusher · Cloudinary | Aplicativo de mensagens (estilo Messenger) com conversas privadas e em grupo, status de presença online e confirmação de leitura em tempo real |

### Nível V — Fundamentos
*Autenticação, banco de dados, pagamento e upload bem executados — a base sólida de qualquer aplicação real.*

| # | Projeto | Stack | Destaque arquitetural |
|---|---|---|---|
| 19 | [Beatstream](https://github.com/renatomf/nextjs-beatstream) | Next.js 14 · Supabase · Stripe · Zustand | Plataforma de streaming de música (estilo Spotify) com upload de faixas próprias, player persistente entre páginas e assinatura premium via Stripe |
| 20 | [Polaris](https://github.com/renatomf/nextjs-polaris) | Next.js 16 · CodeMirror 6 · Vercel AI SDK · Convex · Inngest | Editor de código no navegador (estilo Cursor) com assistente de IA integrado, combinando CodeMirror para edição e um agente conectado ao Google Gemini para assistência contextual |

### Nível VI — Em Desenvolvimento
*Base de autenticação e dados já estruturada, funcionalidades principais em construção.*

| # | Projeto | Stack | Status |
|---|---|---|---|
| 21 | [MediMeet](https://github.com/renatomf/nextjs-medimeet) | Next.js 15 · Prisma · Clerk · React Hook Form | Base de autenticação, formulários e persistência já configurada para uma plataforma de agendamento médico/telemedicina |
| 22 | [Streamly](https://github.com/renatomf/nextjs-streamly) | Next.js 14 · Clerk | Base de autenticação configurada para uma futura plataforma de transmissão ao vivo (estilo Twitch) |

