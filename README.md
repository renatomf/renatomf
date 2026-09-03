<div align="center">

# Olá, eu sou o Renato 👋

Desenvolvedor front-end & full-stack com foco em Next.js, React, Typescript, arquitetura de sistemas e produtos com IA.

[![GitHub followers](https://img.shields.io/github/followers/renatomf?style=social)](https://github.com/renatomf)

</div>

---

### 🔴 Sistemas com problema de engenharia próprio

Projetos onde a parte difícil foi **construída**, não apenas consumida de uma plataforma pronta.

| # | Projeto | Destaque arquitetural |
|---|---|---|
| 1 | [nextjs-nodebase](https://github.com/renatomf/nextjs-nodebase) | Motor de execução durável (Inngest) com resolução de grafo de nós — infraestrutura de execução própria, não só orquestração de serviços |
| 2 | [nextjs-echo](https://github.com/renatomf/nextjs-echo) | Monorepo com 2 aplicações + backend realtime compartilhado (Convex) + RAG + tool calling + escalonamento pra humano |
| 3 | [nextjs-resonance](https://github.com/renatomf/nextjs-resonance) | Storage assinado (R2), billing por uso e cliente de API auto-gerado a partir de um spec OpenAPI externo |
| 4 | [nextjs-meet-ai](https://github.com/renatomf/nextjs-meet-ai) | Separação entre resposta de voz em tempo real (síncrono) e resumo pós-reunião (assíncrono) |
| 5 | [nextjs-craftAI](https://github.com/renatomf/nextjs-craftAI) | Agente de IA que escreve e executa código em sandbox isolado (E2B), com preview ao vivo |
| 6 | [nextjs-teamflow](https://github.com/renatomf/nextjs-teamflow) | Plataforma de mensagens em equipe com segurança de API real: oRPC + Arcjet (bot/rate-limit) + Kinde |
| 7 | [sendkit](https://github.com/renatomf/sendkit) | Uma lógica core exposta por 3 superfícies (CLI / MCP stdio / MCP HTTP+OAuth) |

### 🟠 Peça interna que exige entender um problema difícil

O produto resolve algo já conhecido, mas tem um componente que exige domínio de um problema técnico não trivial.

| # | Projeto | O problema difícil | Por que não é trivial |
|---|---|---|---|
| 8 | [nextjs-docly](https://github.com/renatomf/nextjs-docly) | Edição concorrente sem conflito, via CRDT (Yjs sobre Liveblocks) | Múltiplos usuários editam o mesmo texto ao mesmo tempo e o merge acontece **sem lock e sem "última escrita vence"** — o algoritmo precisa convergir para o mesmo estado em qualquer ordem de chegada dos eventos |
| 9 | [nextjs-sketchpad](https://github.com/renatomf/nextjs-sketchpad) | Mesmo problema de CRDT, aplicado a um **grafo de objetos geométricos** (formas, camadas, posição) em vez de texto linear | Sincronizar texto é difícil; sincronizar posição/z-index/seleção de múltiplos objetos 2D em tempo real, com undo/redo compartilhado, é uma classe de problema maior |
| 10 | [nextjs-vidflow](https://github.com/renatomf/nextjs-vidflow) | Pipeline assíncrono de mídia: upload → transcodificação externa (Mux) → callback assinado (Svix) → job em fila (Upstash Workflow) → geração de metadados por IA → publicação | Cada etapa pode falhar ou atrasar independente das outras — o desafio é orquestrar um pipeline de **múltiplos sistemas externos com estados intermediários**, não só chamar uma API |
| 11 | [nextjs-temu-clone](https://github.com/renatomf/nextjs-temu-clone) | Autenticação implementada do zero (hashing e tokens de sessão via primitivas Oslo, estilo Lucia Auth) em vez de um provedor terceirizado | Fazer auth "funcionar" é fácil; fazer auth **segura** (hashing correto, expiração de sessão, proteção contra fixation/replay) exige entender criptografia aplicada, não só integrar um SDK |

### 🟡 Pipelines assíncronos e múltiplas integrações

Aplicações onde vários serviços externos precisam ser orquestrados em conjunto, com dados fluindo entre eles em etapas.

| # | Projeto | Destaque arquitetural |
|---|---|---|
| 12 | [nextjs-skillup](https://github.com/renatomf/nextjs-skillup) | Vídeo (Mux) + pagamento único (Stripe) + fluxo de publicação com validação de requisitos mínimos |
| 13 | [nextjs-signalist](https://github.com/renatomf/nextjs-signalist) | Jobs em background (Inngest) consultando API externa de mercado e disparando notificações por e-mail |
| 14 | [nextjs-flux](https://github.com/renatomf/nextjs-flux) | Mensagens em tempo real + voz/vídeo (LiveKit) + fallback automático WebSocket→polling |

### 🟢 Fluxos completos, com integrações padrão de mercado

Aplicações funcionais que cobrem um caso de uso real ponta a ponta, com boas práticas de integração já estabelecidas na indústria.

| # | Projeto | Destaque arquitetural |
|---|---|---|
| 15 | [nextjs-havn](https://github.com/renatomf/nextjs-havn) | Busca com mapa interativo (Leaflet) e verificação de conflito de disponibilidade nas reservas |
| 16 | [nextjs-taskify](https://github.com/renatomf/nextjs-taskify) | Drag-and-drop de tarefas com organizações multi-tenant, limite de plano e upgrade via Stripe |
| 17 | [nextjs-lingo](https://github.com/renatomf/nextjs-lingo) | Sistema de gamificação (pontos/vidas) com painel administrativo (React Admin) e assinatura via Stripe |
| 18 | [nextjs-blip](https://github.com/renatomf/nextjs-blip) | Mensagens em tempo real via Pusher, com upload de imagem (Cloudinary) e status de presença |

### 🔵 Fundamentos sólidos, sem desafio de arquitetura em destaque

Projetos que funcionam bem e cobrem o essencial de um app real (auth, banco, pagamento, upload), mas onde nenhuma peça específica exige uma solução de engenharia fora do padrão — o valor aqui está mais na **execução limpa do óbvio** do que em resolver algo difícil.

| # | Projeto | O que entrega | O que faltaria para subir de faixa |
|---|---|---|---|
| 19 | [nextjs-beatstream](https://github.com/renatomf/nextjs-beatstream) | Upload e streaming de áudio, player client-side, curtidas e assinatura via Stripe — um app de mídia completo e funcional | Hoje o áudio é servido direto do storage; algo como streaming adaptativo, fila de reprodução persistida entre sessões, ou processamento de áudio (normalização, waveform) elevaria a complexidade real |
| 20 | [nextjs-polaris](https://github.com/renatomf/nextjs-polaris) | Editor de código com IA integrada, com uma stack rica (CodeMirror, AI SDK, Convex, Inngest) | Só analisei o `package.json` e a estrutura de pastas, não o código da parte que mais importa: **como o agente de IA de fato edita o código no editor**. Sem ver essa peça implementada, não dá pra confirmar se ela justifica ficar entre os projetos de nível mais alto — pode ser que sim, mas ainda não está provado |

### ⚪ Estágio inicial

| # | Projeto | Status |
|---|---|---|
| 21 | [nextjs-medimeet](https://github.com/renatomf/nextjs-medimeet) | Só auth + formulários + Prisma configurados — agendamento e videochamada ainda não implementados |
| 22 | [nextjs-streamly](https://github.com/renatomf/nextjs-streamly) | Só scaffold de autenticação + UI base — streaming ao vivo ainda não implementado |

---

## 🛠️ Stack que mais uso

`Next.js` · `TypeScript` · `React` · `tRPC` / `oRPC` · `Prisma` · `PostgreSQL` · `Convex` · `Clerk` · `Inngest` · `Tailwind CSS`

---

<div align="center">

📫 Entre em contato ou explore os repositórios acima para ver os READMEs detalhados de cada arquitetura.

</div>
