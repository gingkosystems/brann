README — BRANN (Agente Geral da Ginkgo)

BRANN é o agente de IA da Ginkgo que atende pelo WhatsApp, entendendo intenções, respondendo de forma humanizada e registrando tudo na Supabase. Ele usa n8n, Redis e Postgres para orquestrar memória, fluxo e persistência.

🚀 Visão Geral

Entrada: WhatsApp via Evolution API

Motor: Fluxo no n8n (roteador + memória + core LLM)

Cérebro: LLM com prompt mestre e persona BRANN

Dados: Supabase (leads, conversas, agendamentos), Redis (memória curta), Postgres (persistência)

Saída: Resposta no WhatsApp (texto, botões, mídia)

Objetivo: atendimento objetivo, simpático e prático, levando o cliente a três caminhos principais:

Assinatura Ginkgo (planos)

Agendar Consultoria

Falar com Humano

🧩 Arquitetura
WhatsApp (Evolution API)
        │
        ▼
   [Webhook IN] → [Normalizador]
        │
        ▼
   [Roteador de Intenção]
        │
        ├─► [Core LLM BRANN]
        │       │
        │       ├─► Supabase (leads, conversas, tickets)
        │       ├─► Agenda (slots)
        │       └─► FAQ/Docs
        │
        ▼
   [Persistência + Memória]
        │
        ▼
   [Send Message Evolution] → WhatsApp

🎭 Persona do BRANN

Tom: objetivo, simpático, direto

Estilo: natural, frases curtas, sem jargão

Missão: captar intenção → dar resposta clara → oferecer CTA (botões)

Regras:

Nunca deixar a conversa morrer

Confirmar dados essenciais do lead (nome, telefone, origem)

Oferecer sempre opções: planos, consultoria ou humano

⚙️ Estrutura de Código
/brann
  /prompts
    core_brann.md      # persona e FAQ editável
  /n8n
    brann_workflow.json  # fluxo n8n exportado
  /schemas
    supabase.sql       # tabelas leads, conversas, agendamentos
  /docs
    README.md          # este arquivo

🔑 Variáveis de Ambiente
# Evolution API
EVOLUTION_BASE_URL=https://seu-endpoint
EVOLUTION_TOKEN=xxxx
EVOLUTION_INSTANCE=BRANN
ADMIN_WA_ID=55XXXXXXXXXXX

# Supabase
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_ANON_KEY=...
SUPABASE_SERVICE_ROLE=...

# Redis
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=

# n8n
N8N_ENCRYPTION_KEY=chaveforte32chars
N8N_WEBHOOK_URL=https://n8n.suaurl.com/webhook

🗄️ Banco (Supabase)

Leads: dados básicos (phone, nome, origem, estágio)

Conversas: log (in/out, conteúdo, intenção, meta)

Preferências: flags (quer plano, consultoria, orçamento)

Agendamentos: slots, status, tipo (demo, consultoria)

🤖 Fluxo Passo a Passo

Webhook IN: recebe mensagem da Evolution.

Normalizador: extrai texto, áudio → texto, mídia.

Memória curta: puxa últimas interações no Redis.

Roteador: direciona intenção (planos, suporte, humano).

Core BRANN: decide resposta + chama ferramentas (Supabase, Agenda, FAQ).

Persistência: salva conversa, atualiza lead, registra evento.

Saída: envia texto e botões (planos, consultoria, humano).

Follow-ups: lembretes via cron n8n.

📝 Contrato de Mensagem

Entrada:

{
  "from": "55XXXXXXXXXXX",
  "text": "Quais planos vocês têm?"
}


Saída:

{
  "reply_text": "Temos três planos principais (Básico, Avançado, Futurista) e também On-Demand. Quer ver detalhes ou prefere marcar uma consultoria?",
  "buttons": [
    {"id": "SEE_PLANS", "title": "Ver Planos"},
    {"id": "BOOK", "title": "Agendar Consultoria"},
    {"id": "HUMAN", "title": "Falar com Humano"}
  ]
}

✅ Checklist de Ativação

 .env configurado

 Tabelas criadas na Supabase

 Workflow importado no n8n

 Webhook Evolution apontado para o n8n

 Teste inicial: enviar “Oi” → receber saudação + botões

 Handoff humano funcionando (ADMIN_WA_ID recebe alerta)

🔮 Roadmap

Integração de pagamentos (checkout direto no plano)

RAG com base de conhecimento Ginkgo

Dashboard UI em tempo real (leads + conversas)
