# ArchFlow

> **Ferramenta de diagramas de arquitetura de sistemas com IA para desenvolvedores.**  
> Projete, visualize e compartilhe a arquitetura do seu sistema em uma área de trabalho infinita — com diagramas em código, geração por IA e colaboração em tempo real.

---

## Sumário

- [Visão geral](#visão-geral)
- [Recursos](#recursos)
- [Stack tecnológica](#stack-tecnológica)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Primeiros passos](#primeiros-passos)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Fases de desenvolvimento](#fases-de-desenvolvimento)
- [Store do Redux](#store-do-redux)
- [Tipos de nó](#tipos-de-nó)
- [Atalhos do teclado](#atalhos-do-teclado)
- [Rotas da API](#rotas-da-api)
- [Esquema do banco de dados](#esquema-do-banco-de-dados)
- [Scripts](#scripts)
- [Contribuindo](#contribuindo)

---

## Visão geral

O ArchFlow é uma ferramenta de diagramas de arquitetura open source, focada em desenvolvedores e inspirada no [Eraser.io](https://www.eraser.io). Ele permite que desenvolvedores arrastem, conectem e rotulem componentes do sistema em uma tela infinita — além de suportar diagramas em código via Mermaid.js, geração de diagramas com IA e links compartilháveis apenas para leitura.

```
Prompt → IA → Canvas → Exportar / Compartilhar
Código → Mermaid → Canvas (sincronização bidirecional)
Arrastar → Soltar → Conectar → Projetar
```

---

## Recursos

### Fase 1 — MVP do canvas
- ✅ Canvas infinito com zoom e pan (React Flow)
- ✅ 7 tipos de nós arrastáveis na paleta lateral
- ✅ Arestas personalizadas com rótulos (bezier / straight / smoothstep)
- ✅ Painel de propriedades do nó (rótulo, cor, descrição)
- ✅ Desfazer / Refazer (pilha past/future do Redux)
- ✅ Exportação em PNG
- ✅ Auto-save no localStorage
- ✅ Atalhos do teclado

### Fase 2 — Diagramas em código
- ✅ Painel do editor Monaco com sintaxe Mermaid
- ✅ Sincronização bidirecional: canvas ↔ código
- ✅ Visualização em tempo real do Mermaid
- ✅ Alternar painel de código aberto/fechado

### Fase 3 — Autenticação + Persistência
- ✅ Autenticação com NextAuth.js (Google + Email)
- ✅ Salvar diagramas no PostgreSQL via Prisma
- ✅ Dashboard — listar, renomear, excluir e duplicar diagramas
- ✅ Auto-save no banco de dados em mudança (debounced)

### Fase 4 — Geração com IA
- ✅ Prompt → diagrama (API Claude)
- ✅ IA explica o nó selecionado
- ✅ Interface com resposta em streaming

### Fase 5 — Compartilhamento + Templates
- ✅ Links compartilháveis somente leitura
- ✅ Galeria de templates (microserviços, monolito, orientado a eventos, CQRS)
- ✅ Incorporar diagramas via iframe

---

## Stack tecnológica

| Camada | Tecnologia |
|---|---|
| Framework | Next.js 15 (App Router) |
| Linguagem | TypeScript |
| Estilização | Tailwind CSS |
| Canvas | React Flow |
| Editor de código | Monaco Editor |
| Diagramas em código | Mermaid.js |
| Gerenciamento de estado | Redux Toolkit + React Redux |
| Autenticação | NextAuth.js |
| ORM do banco | Prisma |
| Banco de dados | PostgreSQL |
| IA | Claude API (Anthropic) |
| Geração de IDs | nanoid |
| Gerenciador de pacotes | npm |

---

## Estrutura do projeto

```
archflow/
├── public/
│   ├── icons/
│   │   ├── nodes/                        # Ícones SVG dos tipos de nó
│   │   └── clouds/                       # Ícones AWS / GCP / Azure
│   └── templates/                        # Templates de diagramas em JSON
│
├── prisma/
│   ├── schema.prisma
│   └── migrations/
│
├── src/
│   ├── app/
│   │   ├── (auth)/                       # Páginas de login / cadastro
│   │   ├── dashboard/                    # Lista de diagramas
│   │   ├── editor/[diagramId]/           # Editor principal do canvas
│   │   ├── shared/[shareId]/             # Visualização compartilhada somente leitura
│   │   └── api/                          # Rotas da API
│   │       ├── auth/[...nextauth]/
│   │       ├── diagrams/[diagramId]/
│   │       └── ai/generate | explain
│   │
│   ├── components/
│   │   ├── canvas/                       # CanvasBoard, controles, arestas
│   │   ├── nodes/                        # Todos os componentes de tipos de nó
│   │   ├── sidebar/                      # NodePalette, PaletteItem
│   │   ├── toolbar/                      # Barra superior, exportar, compartilhar, IA
│   │   ├── properties/                   # Painéis de propriedades de nó/aresta
│   │   ├── code-panel/                   # Monaco + visualização Mermaid
│   │   ├── auth/                         # Formulários de login/cadastro
│   │   ├── dashboard/                    # DiagramCard, DiagramGrid
│   │   ├── ai/                           # Modal de prompt IA, painel de explicação
│   │   ├── share/                        # Modal de compartilhamento, banner somente leitura
│   │   ├── templates/                    # Galeria de templates
│   │   └── ui/                           # Button, Modal, Input, Tooltip...
│   │
│   ├── store/
│   │   ├── index.ts                      # configureStore
│   │   ├── hooks.ts                      # useAppDispatch, useAppSelector
│   │   └── slices/
│   │       ├── diagramSlice.ts           # nós, arestas, undo/redo
│   │       ├── uiSlice.ts                # sidebar, painéis, selecionado
│   │       ├── authSlice.ts              # sessão do usuário
│   │       ├── diagramsSlice.ts          # lista do dashboard
│   │       └── aiSlice.ts                # prompt, loading, resultado
│   │
│   ├── hooks/
│   │   ├── useCanvasDrop.ts              # Palette → canvas drop
│   │   ├── useExport.ts                  # Exportação PNG
│   │   ├── useUndoRedo.ts                # Desfazer/refazer com teclado
│   │   ├── useAutoSave.ts                # localStorage / salvar no banco
│   │   ├── useMermaidSync.ts             # Sincronização código ↔ canvas
│   │   ├── useDiagrams.ts                # Buscar lista de diagramas
│   │   ├── useAiGenerate.ts              # Chamada à API de IA
│   │   └── useShareLink.ts               # Gerar URL compartilhável
│   │
│   ├── lib/
│   │   ├── reactflow/                    # nodeTypes, edgeTypes, viewport
│   │   ├── mermaid/                      # mermaidToFlow, flowToMermaid
│   │   ├── ai/                           # promptBuilder, responseParser
│   │   ├── db/                           # Prisma client singleton
│   │   └── auth/                         # Opções do NextAuth
│   │
│   ├── utils/
│   │   ├── nodeFactory.ts                # Criar nó RF por tipo
│   │   ├── edgeFactory.ts                # Criar aresta RF com valores padrão
│   │   ├── generateId.ts                 # wrapper do nanoid
│   │   ├── downloadFile.ts               # Dispara download no navegador
│   │   └── cn.ts                         # Helper Tailwind clsx
│   │
│   ├── types/
│   │   ├── diagram.ts                    # Node, Edge, NodeType
│   │   ├── auth.ts                       # User, Session
│   │   ├── api.ts                        # Requisição/resposta da API
│   │   └── ai.ts                         # Prompt/resposta da IA
│   │
│   └── constants/
│       ├── nodeDefaults.ts               # Rótulo/cor padrão por tipo
│       ├── edgeDefaults.ts               # Estilo padrão da aresta
│       ├── paletteGroups.ts              # Configuração de agrupamento da sidebar
│       └── shortcuts.ts                  # Mapa de atalhos do teclado
│
├── .env.local
├── .env.example
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
└── package.json
```

---

## Primeiros passos

### Pré-requisitos

- Node.js 18+
- Banco PostgreSQL
- npm

### Instalação

```bash
# 1. Clone o repositório
git clone https://github.com/yashdark01/archflow.git
cd archflow

# 2. Instale as dependências
npm install reactflow @monaco-editor/react mermaid @reduxjs/toolkit react-redux

# 3. Instale os pacotes de utilidades e desenvolvimento
npm install nanoid clsx tailwind-merge

# 4. Instale os pacotes de autenticação e banco (Fase 3)
npm install next-auth @prisma/client prisma

# 5. Copie as variáveis de ambiente
cp .env.example .env.local

# 6. Configure o banco (Fase 3)
npx prisma generate
npx prisma migrate dev --name init

# 7. Inicie o servidor de desenvolvimento
npm run dev
```

Abra [http://localhost:3000](http://localhost:3000)

---

## Variáveis de ambiente

```env
# .env.example

# App
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_secret_here

# Banco de dados (Fase 3)
DATABASE_URL=postgresql://user:password@localhost:5432/archflow

# Provedores de autenticação (Fase 3)
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret

# IA (Fase 4)
ANTHROPIC_API_KEY=your_anthropic_api_key
```

---

## Fases de desenvolvimento

### Fase 1 — Canvas MVP *(Semanas 1–3)*
Canvas principal com nós arrastáveis, conexões, painel de propriedades, desfazer/refazer, exportação em PNG e auto-save no localStorage.

```bash
# Só precisa da instalação base
npm run dev
```

### Fase 2 — Diagramas em código *(Semanas 4–5)*
Editor Monaco com Mermaid.js. Sincronização bidirecional entre código e canvas.

```bash
npm install mermaid @monaco-editor/react
```

### Fase 3 — Autenticação + Persistência *(Semana 6)*
Login com NextAuth, PostgreSQL via Prisma e dashboard de diagramas.

```bash
npm install next-auth @prisma/client prisma
npx prisma migrate dev --name init
```

### Fase 4 — Geração com IA *(Semana 7)*
Integração com a API Claude. Prompt → diagrama, explicação do nó.

```bash
# Adicione ANTHROPIC_API_KEY ao .env.local
```

### Fase 5 — Compartilhamento + Templates *(Semana 8)*
Links somente leitura, galeria de templates e incorporação via iframe.

---

## Store do Redux

### Estrutura do estado

```ts
// diagramSlice — estado do canvas
{
  nodes: Node[],
  edges: Edge[],
  past:   Array<{ nodes: Node[], edges: Edge[] }>,  // pilha de undo
  future: Array<{ nodes: Node[], edges: Edge[] }>,  // pilha de redo
}

// uiSlice — estado da interface
{
  selectedNodeId:      string | null,
  selectedEdgeId:      string | null,
  sidebarOpen:         boolean,
  propertiesPanelOpen: boolean,
  codePanelOpen:       boolean,
  activeEdgeType:      'bezier' | 'straight' | 'smoothstep',
}

// authSlice — sessão do usuário (Fase 3)
{
  user:   { id, name, email, image } | null,
  status: 'idle' | 'loading' | 'authenticated' | 'unauthenticated',
}

// diagramsSlice — lista do dashboard (Fase 3)
{
  diagrams: Diagram[],
  status:   'idle' | 'loading' | 'succeeded' | 'failed',
  error:    string | null,
}

// aiSlice — estado da IA (Fase 4)
{
  prompt:  string,
  status:  'idle' | 'loading' | 'succeeded' | 'failed',
  result:  string | null,
  error:   string | null,
}
```

### Ações principais

```ts
// diagramSlice
diagramSlice.actions.addNode(node)
diagramSlice.actions.updateNode({ id, changes })
diagramSlice.actions.removeNode(id)
diagramSlice.actions.addEdge(edge)
diagramSlice.actions.removeEdge(id)
diagramSlice.actions.setNodes(nodes)
diagramSlice.actions.setEdges(edges)
diagramSlice.actions.undo()
diagramSlice.actions.redo()
diagramSlice.actions.loadDiagram({ nodes, edges })

// uiSlice
uiSlice.actions.setSelectedNode(id)
uiSlice.actions.setSelectedEdge(id)
uiSlice.actions.toggleSidebar()
uiSlice.actions.toggleCodePanel()
uiSlice.actions.setActiveEdgeType(type)
```

---

## Tipos de nó

| Tipo | Descrição | Forma |
|---|---|---|
| `service` | Microserviço genérico / aplicação | Retângulo |
| `database` | Banco SQL / NoSQL | Cilindro |
| `queue` | Fila de mensagens / barramento de eventos | Retângulo + setas |
| `cache` | Redis / Memcached | Retângulo |
| `apiGateway` | API Gateway / proxy reverso | Losango |
| `loadBalancer` | Balanceador de carga | Trapézio |
| `user` | Ator / usuário final | Círculo |
| `group` | Contêiner de limite (VPC, cluster) | Retângulo tracejado |
| `cloud` | Nó de serviço AWS / GCP / Azure | Caixa com ícone |

Cada nó suporta:
- Rótulo editável (duplo clique)
- Handles de origem/destino em todos os 4 lados
- Cor personalizada via painel de propriedades
- Campo de descrição
- Destaque visual quando selecionado

---

## Atalhos do teclado

| Atalho | Ação |
|---|---|
| `Ctrl + Z` | Desfazer |
| `Ctrl + Shift + Z` | Refazer |
| `Ctrl + A` | Selecionar tudo |
| `Delete` / `Backspace` | Excluir nó ou aresta selecionada |
| `Escape` | Desmarcar tudo |
| `Ctrl + C` | Copiar nós selecionados |
| `Ctrl + V` | Colar nós |
| `Ctrl + D` | Duplicar nós selecionados |
| `Ctrl + E` | Exportar como PNG |
| `Ctrl + /` | Alternar painel de código |
| `Space + Drag` | Mover o canvas |
| `Scroll` | Aproximar / afastar |

---

## Rotas da API

### Diagramas

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/api/diagrams` | Obter todos os diagramas do usuário |
| `POST` | `/api/diagrams` | Criar novo diagrama |
| `GET` | `/api/diagrams/[id]` | Obter um único diagrama |
| `PUT` | `/api/diagrams/[id]` | Atualizar diagrama |
| `DELETE` | `/api/diagrams/[id]` | Excluir diagrama |
| `POST` | `/api/diagrams/[id]/share` | Gerar link de compartilhamento |

### IA

| Método | Rota | Descrição |
|---|---|---|
| `POST` | `/api/ai/generate` | Prompt → nós/arestas do diagrama |
| `POST` | `/api/ai/explain` | Explicar o nó selecionado |

### Exemplos de requisição / resposta

```ts
// POST /api/ai/generate
{
  "prompt": "Crie uma arquitetura de microsserviços para um aplicativo de e-commerce"
}

// Resposta
{
  "nodes": [...],
  "edges": [...]
}

// POST /api/diagrams
{
  "title": "Minha Arquitetura",
  "nodes": [...],
  "edges": [...]
}
```

---

## Esquema do banco de dados

```prisma
// prisma/schema.prisma

model User {
  id        String    @id @default(cuid())
  name      String?
  email     String    @unique
  image     String?
  diagrams  Diagram[]
  createdAt DateTime  @default(now())
}

model Diagram {
  id        String   @id @default(cuid())
  title     String   @default("Untitled Diagram")
  nodes     Json
  edges     Json
  shareId   String?  @unique
  userId    String
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

---

## Scripts

```bash
# Desenvolvimento
npm run dev           # Inicia o servidor em localhost:3000

# Build
npm run build         # Build de produção
npm run start         # Inicia o servidor de produção

# Banco de dados
npx prisma generate           # Gera o cliente Prisma
npx prisma migrate dev        # Executa migrations em desenvolvimento
npx prisma migrate deploy     # Executa migrations em produção
npx prisma studio             # Abre a interface gráfica do Prisma Studio

# Linting
npm run lint          # Verificação do ESLint
npm run lint:fix      # Correção automática do ESLint
```

---

## Contribuindo

```bash
# Faça um fork e clone
git clone https://github.com/yashdark01/archflow.git

# Crie uma branch para a funcionalidade
git checkout -b feature/nome-da-sua-feature

# Faça suas alterações e então faça o commit
git commit -m "feat: add your feature"

# Envie e abra um PR
git push origin feature/nome-da-sua-feature
```

### Nomeação de branches

| Prefixo | Uso |
|---|---|
| `feature/` | Novas funcionalidades |
| `fix/` | Correções de bugs |
| `phase/` | Trabalho por fase |
| `docs/` | Apenas documentação |
| `refactor/` | Reestruturação de código |

---

## Roadmap

- [ ] Fase 1 — Canvas MVP
- [ ] Fase 2 — Diagramas em código (Mermaid)
- [ ] Fase 3 — Autenticação + PostgreSQL
- [ ] Fase 4 — Geração com IA (Claude API)
- [ ] Fase 5 — Compartilhamento + Templates
- [ ] Fase 6 — Importação do GitHub (repositório → arquitetura)
- [ ] Fase 7 — Colaboração em tempo real (WebSockets)
- [ ] Fase 8 — Extensão para VS Code

---

## Licença

MIT © [Yash Patidar](https://github.com/yashdark01)