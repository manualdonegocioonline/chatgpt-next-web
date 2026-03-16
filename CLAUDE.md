# CLAUDE.md — Guia Técnico para Assistentes de IA: Estagiário.ai

Este documento fornece contexto completo para assistentes de IA (Claude, Copilot, etc.) que trabalham neste codebase.

---

## O que é o Estagiário.ai

O **Estagiário.ai** é uma plataforma web de gestão documental e jurídica inteligente, desenvolvida para escritórios de advocacia de pequeno e médio porte e empresas com alto volume de documentos. Funciona como um estagiário digital autônomo: recebe arquivos, interpreta comandos em linguagem natural, executa tarefas operacionais e auxilia na produção jurídica — liberando o advogado para o trabalho estratégico.

---

## Stack Tecnológica

| Camada | Tecnologia |
|---|---|
| Frontend | HTML5 + CSS3 + JavaScript vanilla (arquivo único) |
| Tipografia display | Cormorant Garamond (serif editorial) |
| Tipografia interface | Geist (sans-serif técnica) |
| Tipografia mono | Geist Mono |
| IA / Chat | Anthropic Claude API |
| Agendamento | Google Calendar API |
| Armazenamento | Google Drive / Google Tasks |
| Extração de texto | OCR (PDFs e imagens → full-text) |
| Automações | Zapier / Make |

**Base de código:** Este repositório é derivado do ChatGPT-Next-Web e adaptado para as necessidades jurídicas do Estagiário.ai.

---

## Identidade Visual (Design System)

| Elemento | Valor |
|---|---|
| Background | `#fafafa` — papel off-white |
| Superfícies | `white` com `border: 1px solid rgba(0,0,0,0.07)` |
| Accent principal | `#0066ff` — azul puro |
| Tipografia display | Cormorant Garamond |
| Tipografia interface | Geist |
| Tipografia mono | Geist Mono |
| Sombras | 4 níveis de elevação, sempre `rgba(0,0,0,x)` |
| Border radius | `10px` · `14px` · `18px` |
| Estética | Linear + Stripe + Vercel — minimalismo de precisão |

**Nunca introduzir:**
- Background preto ou escuro em superfícies principais
- Cores de destaque além do `#0066ff` e variantes semânticas (vermelho erro, verde sucesso, âmbar alerta)
- Fontes externas além de Cormorant Garamond e Geist

---

## Arquitetura de Páginas

### 1. Hoje (Dashboard — `/`)

Página inicial com visão consolidada do dia.

**Componentes:**
- **Hero de briefing IA:** bloco escuro com resumo gerado automaticamente (audiências do dia, prazos fatais, documentos pendentes, tarefas prioritárias)
- **4 cards de métricas:** atividades do dia · audiências · documentos pendentes · concluídas
- **Lista de atividades do dia:** ordenadas por prioridade (Alta / Média / Baixa) e esforço estimado, com checkbox interativo
- **Agenda do dia:** linha do tempo com horários, tipos de evento coloridos, local/vara
- **Log do Estagiário:** registro cronológico de tudo que a IA executou

### 2. Atividades (Kanban — `/atividades`)

Sistema Kanban de tarefas jurídicas.

**4 colunas:**
1. Urgente/Hoje
2. Em andamento
3. Aguardando
4. Concluído

**Cards contêm:** título, área do direito, tribunal, prazo, esforço estimado, indicador de prioridade colorido.

**Lógica de priorização automática:**
```
prazo fatal > audiência > produção jurídica > administrativo
```
Em empate de prioridade, tarefas de menor esforço aparecem acima (quick wins).

**Modal de nova atividade:** título, prioridade, esforço, prazo, área, cliente, opção de criar lembrete no Google Calendar.

**Toggle:** visualização lista ↔ kanban.

### 3. Agenda & Audiências (`/agenda`)

**Componentes:**
- Calendário mensal visual com marcação por tipo/cor
- Lista de próximas audiências e prazos com horário, tribunal, nº processo

**Tipos e cores de evento:**

| Tipo | Cor |
|---|---|
| Audiência judicial | Vermelho |
| Prazo processual | Âmbar |
| Reunião cliente | Verde |
| Reunião interna | Azul |
| Trabalhistas | Violeta |

**Parsing automático:** ao enviar foto ou PDF de intimação no Chat Principal, a IA extrai data, hora, tribunal e processo e pré-preenche o evento.

**Modal de agendamento:** título, tipo, data, horário, local/vara, processo/referência, sincronização Google Calendar, lembrete antecipado, notificação para sócios.

### 4. Chat Principal (`/chat`)

Interface de comando central — visual inspirado no Claude.ai.

**Layout:**
- Chat estilo LLM: histórico, avatares, balões diferenciados (IA: fundo escuro / usuário: azul `#0066ff`)
- Textarea expansiva: Enter para enviar, Shift+Enter para nova linha
- Upload: botão de anexar arquivo + botão câmera, chips de preview dos anexos na área de input

**Capacidades do Estagiário via chat:**
- Catalogar e mover arquivos para pastas de clientes
- Criar atividades no Kanban com prioridade automática
- Agendar eventos no Google Calendar
- Localizar documentos por cliente, tipo ou data
- Acionar o Assessor Jurídico para redigir uma peça
- Renomear arquivos em lote com padrão jurídico
- Mesclar PDFs
- Classificar documentos pendentes

**A IA confirma cada ação com feedback claro** — nunca executa sem resposta ao usuário.

### 5. Assessor Jurídico (`/assessor`)

Ferramenta de redação de peças processuais com layout **split-panel**.

#### Painel esquerdo — Chat do Assessor

- Header escuro: "Assessor Jurídico · especialista em legislação brasileira vigente"
- Chat dedicado à redação: usuário descreve caso, partes, pedidos, fatos
- Botão de anexar documentos do cliente
- Refinamentos via chat: "inclua o art. 7º da CF", "ajuste o valor dos alimentos", "adicione pedido de tutela de urgência"

**Peças suportadas:**
- Petição inicial
- Contestação
- Recurso ordinário
- Apelação
- Agravo
- Habeas corpus
- Mandado de segurança
- Embargos de declaração
- Procuração
- Contrato
- Parecer
- Notificação extrajudicial
- Requerimento de inventário

#### Painel direito — Editor Word-like

- Barra de ferramentas: negrito, itálico, sublinhado, alinhamento, listas, fonte, tamanho
- Documento editável (`contenteditable`)
- **Campos pendentes marcados em azul** com `[CONFIRMAR COM CLIENTE]`
- Indicador de status na toolbar (verde = salvo)
- Botão de download `.docx`
- Formatação conforme padrões de tribunais brasileiros: Times New Roman 12pt, espaçamento 1.5

**Peças recentes:** grid de 8 cards com miniatura, nome truncado e pasta/cliente onde está salva.

### 6. Meus Arquivos (`/arquivos`)

Gestão completa do acervo documental.

**Zona de upload:** drag & drop, suporte a PDF, DOCX, XLSX, JPG/PNG.

**Barra de progresso de catalogação:** percentual em tempo real enquanto a IA processa.

**Árvore de pastas padrão:**
```
CLIENTES EM ANDAMENTO/
  └── [nome do cliente]/
PEÇAS PROCESSUAIS/
  ├── Petições/
  ├── Contestações/
  └── Recursos/
CONTRATOS/
  ├── Vigentes/
  └── Encerrados/
ARQUIVO MORTO/
```

**Ações em lote:**
- Renomear com padrão IA: `Cliente_TipoDoc_Data`
- Classificar documentos pendentes
- Detectar e limpar duplicados

> **REGRA CRÍTICA:** Nunca implementar exclusão permanente de arquivos. Apenas mover para `ARQUIVO MORTO/`. Esta regra não tem exceção.

### 7. Esteira de Procuração (`/procuracao`)

Workflow para escritórios com alto volume de procurações.

**6 etapas horizontais (fluxo visual):**
```
01 Solicitação → 02 Minuta IA → 03 Revisão → 04 Assinatura → 05 Reconhecimento → 06 Arquivado
```

- Etapas concluídas: verde
- Etapa atual: azul (`#0066ff`)
- Próximas etapas: neutro

**Tipos de procuração:** ad judicia · ad negotia · plena · especial

**Modal de nova procuração:** outorgante, tipo, poderes específicos, geração automática de minuta pela IA.

### 8. Sobre (`/sobre`)

Landing page interna do produto.

**Componentes:**
- Hero escuro premium com headline em Cormorant Garamond
- Grid de 9 funcionalidades (ícone + nome + descrição)
- Seção de planos:

| Plano | Preço | Limite |
|---|---|---|
| Essencial | R$ 97/mês | Advogado solo |
| Pro | R$ 247/mês | Até 5 usuários |
| Enterprise | Sob consulta | Ilimitado |

---

## Contexto Jurídico Brasileiro

O Estagiário.ai opera exclusivamente no contexto do direito brasileiro. Ao gerar qualquer conteúdo jurídico:

**Tribunais reconhecidos:**
- TJGO (Tribunal de Justiça de Goiás)
- TRT 18ª Região (Trabalhista)
- STJ (Superior Tribunal de Justiça)
- STF (Supremo Tribunal Federal)
- TRF1 (Tribunal Regional Federal da 1ª Região)

**Legislação:** referenciar sempre a legislação vigente no Brasil. Nunca inventar artigos ou leis — se houver incerteza, marcar com `[VERIFICAR LEGISLAÇÃO]`.

**Nomenclatura de arquivos (padrão IA):**
```
NomeCliente_TipoDocumento_YYYY-MM-DD
Exemplo: Anderson_A_Vieira_PeticaoInicial_2025-06-15
```

**Padrão de formatação de peças:**
- Fonte: Times New Roman 12pt
- Espaçamento: 1.5
- Margens: 3cm (superior/esquerda), 2cm (inferior/direita)
- Conforme normas dos tribunais brasileiros

---

## Integrações — Contratos de API

### Anthropic Claude API

Usada para: catalogação, redação de peças, classificação de documentos, chat principal, parsing de intimações.

```typescript
// Modelo padrão para tarefas de produção
const model = "claude-opus-4-6"; // Redação jurídica, análise complexa
const modelFast = "claude-haiku-4-5-20251001"; // Classificação, catalogação, tarefas rápidas
```

**System prompt base para o Assessor Jurídico:**
> Você é um assessor jurídico especializado em legislação brasileira vigente. Redija peças processuais conforme as normas dos tribunais brasileiros, sempre em português formal jurídico. Marque com [CONFIRMAR COM CLIENTE] qualquer dado que precise ser validado com o cliente antes do protocolo.

### Google Calendar API

Usada para: criação de eventos de audiências, prazos, reuniões, lembretes antecipados.

Escopos necessários:
- `https://www.googleapis.com/auth/calendar.events`
- `https://www.googleapis.com/auth/calendar.readonly`

### Google Drive API

Usada para: armazenamento e organização de documentos na árvore de pastas.

---

## Regras de Negócio Críticas

1. **Sem exclusão permanente.** Arquivos só vão para `ARQUIVO MORTO/`. Nunca implementar `DELETE` permanente em arquivos de clientes.

2. **Campos pendentes sempre marcados.** Peças geradas pela IA devem ter dados não confirmados marcados com `[CONFIRMAR COM CLIENTE]` em azul destacado.

3. **Confirmação de toda ação executada.** O Chat Principal sempre exibe feedback claro de cada ação executada pela IA — nunca executa silenciosamente.

4. **Priorização determinística.** A ordem de prioridade para Kanban é sempre: `prazo fatal > audiência > produção > administrativo`. Em empate, menor esforço sobe.

5. **Contexto brasileiro obrigatório.** Toda redação jurídica é em português formal, referenciando apenas legislação e tribunais brasileiros.

6. **Parsing não-destrutivo.** Ao extrair dados de intimações para pré-preencher agenda, sempre exibir para confirmação antes de salvar.

---

## Convenções de Código

### Estrutura de arquivos (frontend vanilla)

O frontend é construído em **arquivo único** (HTML + CSS + JS inline ou em arquivos separados na mesma pasta). Não usar frameworks como React, Vue ou Svelte nesta camada, a menos que explicitamente instruído.

### CSS

- Usar variáveis CSS (`--color-accent`, `--radius-md`, etc.) para todos os valores do design system
- Glassmorphism: `backdrop-filter: blur()` + `background: rgba(255,255,255,0.x)`
- Sombras em camadas:
  ```css
  box-shadow:
    0 1px 2px rgba(0,0,0,0.04),
    0 4px 12px rgba(0,0,0,0.06),
    0 12px 32px rgba(0,0,0,0.08);
  ```

### JavaScript

- Vanilla JS — sem dependências externas além das APIs listadas
- Funções nomeadas em camelCase
- Eventos delegados ao documento quando possível (performance)
- Fetch API para todas as chamadas HTTP

### Commits

- Mensagens em português, modo imperativo: `Adiciona modal de nova procuração`, `Corrige ordenação do Kanban`
- Nunca commitar diretamente em `main`/`master`

---

## O que Não Fazer

- Não usar bibliotecas de UI externas (Material UI, Bootstrap, Tailwind, shadcn)
- Não excluir arquivos permanentemente — sempre mover para arquivo morto
- Não inventar artigos de lei ou jurisprudência
- Não implementar funcionalidades não descritas neste documento sem confirmação
- Não alterar o design system (cores, fontes, raios) sem instrução explícita
- Não expor chaves de API no frontend — sempre proxiar pelo backend
