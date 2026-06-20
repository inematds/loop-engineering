# Loop Engineering — Plano do Curso

## Visão Geral
Curso introdutório à abordagem "Loop Engineering" para sistemas de IA, baseado no vídeo de Cole Medin e pesquisas complementares. Apresenta fundamentos, vantagens, desvantagens honestas, implementação técnica e exemplos práticos com prompts prontos.

## Fonte Principal
- Vídeo: "The Creators of Claude Code and OpenClaw don't Prompt Their Agents Anymore?!" — Cole Medin (24 min)
- Canal: Cole Medin (YouTube)
- Transcrição completa em: /home/nmaldaner/projetos/openpcbot/out/1439d183_transcript.txt

## Personas-chave citadas
- **Boris Cherny** — Head of Claude Code, Anthropic. "I don't prompt Claude anymore. I write loops and the loops do the work."
- **Peter Steinberger** — Creator of OpenClaw. Runs loops that prompt Claude for him.
- **Cole Medin** — Criador do Archon, apresentador do vídeo.

## Estrutura do Curso

### Trilha 1 — Fundamentos do Loop Engineering
O que é, de onde veio, por que está virando buzzword.

- **Módulo 1.1 — O Que É Loop Engineering**
  - Definição: sistema onde loops automatizados fazem o prompting dos agentes, não o humano
  - Diferença entre "prompting manual" vs "loop-driven"
  - Citações de Boris Cherny e Peter Steinberger
  - Analogia: cron jobs + agentes de IA

- **Módulo 1.2 — Os Blocos Fundamentais**
  - /slash loop — intervalos de execução (ex: a cada 5 min)
  - /slash goal — critério de conclusão, agente trabalha até "done"
  - /slash routines — jobs agendados (ex: a cada hora, revisar spec)
  - Como se combinam para formar um sistema de loop

- **Módulo 1.3 — Arquitetura Orchestrator-Workers**
  - Conceito: orchestrator decide, workers executam
  - Fluxo: spec → orchestrator → spin workers → resultados → orchestrator → próxima onda
  - State management via banco externo (Postgres/Neon)
  - Durabilidade e resumabilidade

### Trilha 2 — Vantagens vs Desvantagens (Visão Honesta)
Análise crítica sem hype.

- **Módulo 2.1 — Vantagens Reais**
  - Autonomia: agentes trabalham 24/7 sem intervenção
  - Escala: processar múltiplas tarefas em paralelo (worktrees)
  - Composabilidade: orquestrador + workers = sistema modular
  - Exploração: excelente para provas de conceito e ideação
  - Incrementalidade: tarefas grandes divididas em ciclos pequenos

- **Módulo 2.2 — Desvantagens e Riscos**
  - Custo: >1M tokens para apps simples, orchestrator reasoning é caro
  - Qualidade: "run for a day, come back to crap" — sem HITL, resultados degradam
  - Context bloat: looping na mesma sessão sobrecarrega o LLM
  - Complexidade: engenharia pesada para algo que "não merece seu próprio buzzword"
  - Alucinação composta: erros se propagam entre rodadas
  - Questionamento de Cole: "Boris manages tens of thousands of agents per day — is that practical?"

- **Módulo 2.3 — Quando Usar e Quando Não Usar**
  - Bom para: GitHub issues em batch, exploração, PoCs, tarefas repetitivas
  - Ruim para: código de produção crítico, orçamento limitado, sem observabilidade
  - Framework de decisão: "does this actually need a loop?"
  - Regra de ouro: loops para distribuição, humanos para validação

### Trilha 3 — Implementação Técnica
Mão na massa: como construir sistemas de loop.

- **Módulo 3.1 — Loop Básico com Claude Code**
  - Setup do /loop skill
  - Criando um plan.md com tarefas
  - Prompt para orchestrator configurar o loop sozinho
  - Demo: task list sequencial com wake-up automático
  - Prompt pronto para download

- **Módulo 3.2 — Workflows Determinísticos com Archon**
  - O que é Archon: harness builder para orquestrar sessões de coding agents
  - Workflow "Fix GitHub Issue": extract → fetch → classify → research → implement → validate → PR
  - Diferença-chave: processo determinístico (humano define steps) vs loop puro (agente decide)
  - Mix de modelos: Haiku para classify, Claude para implement, Kimi para exploration
  - Prompts prontos para cada step

- **Módulo 3.3 — Dashboard de Controle (Agent Control Plane)**
  - O que é o Agent Control Plane (open source)
  - Postgres/Neon como state store
  - Observabilidade: ver decisões do orchestrator em tempo real
  - Cost tracking por run
  - Human-in-the-loop: pause + approve antes de próxima rodada
  - Deploy com Retool para acesso remoto e em time

- **Módulo 3.4 — Otimização de Custos**
  - Mix de providers: Pi/Kimi para orchestrator, Claude para implementation
  - Model routing: modelos pequenos onde reasoning é simples
  - State externo: evitar context bloat mantendo estado no banco
  - Worktrees para isolamento de sessões paralelas
  - Batch vs real-time: quando cada um vale a pena
  - Métricas a monitorar: tokens/task, custo/round, taxa de sucesso

### Trilha 4 — Exemplos Práticos e Prompts
Receitas prontas para copiar e usar.

- **Módulo 4.1 — Loop Simples: Task Runner**
  - Prompt: "Load the loop skill, work through plan.md one task at a time"
  - Template de plan.md com 5 tarefas
  - Como monitorar progresso
  - Prompt pack download

- **Módulo 4.2 — Loop Intermediário: GitHub Issue Bot**
  - Setup: orchestrator session + 4 worker workflows paralelos
  - Worktrees para isolamento
  - Database branches com Neon
  - Fluxo completo: issue → fix → PR → review
  - Prompt pack download

- **Módulo 4.3 — Loop Avançado: Orchestrator Multi-Agent**
  - Spec document como input
  - Orchestrator planeja rounds e workers
  - Workers executam e reportam ao state store
  - Orchestrator valida e lança próximo round
  - Human-in-the-loop checkpoints
  - Prompt pack download

- **Módulo 4.4 — Anti-Patterns e Troubleshooting**
  - Loop infinito sem exit condition
  - Context window overflow
  - Port conflicts em sessões paralelas
  - Workers pisando uns nos outros
  - Como debuggar com logs do dashboard
  - Checklist de "ready to loop"

## Manifesto (curso.json)
```json
{
  "id": "loop-engineering",
  "titulo": "Loop Engineering — Agentes em Loop Contínuo",
  "subtitulo": "Da promessa ao controle: como (e quando) usar loops de IA com responsabilidade",
  "trilhas": [
    {
      "id": "trilha1",
      "titulo": "Fundamentos",
      "modulos": ["modulo-1-1", "modulo-1-2", "modulo-1-3"]
    },
    {
      "id": "trilha2",
      "titulo": "Vantagens vs Desvantagens",
      "modulos": ["modulo-2-1", "modulo-2-2", "modulo-2-3"]
    },
    {
      "id": "trilha3",
      "titulo": "Implementação Técnica",
      "modulos": ["modulo-3-1", "modulo-3-2", "modulo-3-3", "modulo-3-4"]
    },
    {
      "id": "trilha4",
      "titulo": "Exemplos Práticos e Prompts",
      "modulos": ["modulo-4-1", "modulo-4-2", "modulo-4-3", "modulo-4-4"]
    }
  ],
  "total_modulos": 14,
  "total_topicos": 84,
  "formato": "formato-curso-v2",
  "learn_layer": true,
  "assets": ["assets/learn.css", "assets/learn.js"]
}
```

## Diretrizes de Conteúdo
- Tom: honesto, prático, sem hype — seguir o tom do Cole Medin
- Exemplos: todos com prompts copiáveis e explicação passo a passo
- Código: TypeScript + Python quando relevante
- Cada módulo tem seção "Prompt Pack" com prompts prontos para download
- Cada módulo tem seção "Questionamentos" levantando dúvidas legítimas
- Formato visual: INEMA dark premium âmbar/cyan, Tailwind CDN, learn layer v2
