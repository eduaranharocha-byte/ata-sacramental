# 📋 Ata Sacramental — Ala Bougainville

> Sistema web colaborativo para registro e gestão das atas da Reunião Sacramental.  
> Desenvolvido para uso interno da **Ala Bougainville · Estaca Anápolis Brasil**.

---

## 📌 Visão Geral

Aplicação de página única (HTML + JavaScript puro) integrada ao **Supabase** como banco de dados em nuvem. Permite que múltiplos líderes preencham e consultem as atas das reuniões dominicais em tempo real, de qualquer dispositivo, sem necessidade de instalação ou servidor próprio.

---

## ✨ Funcionalidades

### 🔐 Autenticação e Controle de Acesso
- Login por **cargo** (Bispo, 1º Conselheiro, 2º Conselheiro, Secretário Executivo, Secretário da Ala)
- Validação por **código da Estaca e da Ala** como camada de segurança
- Limite de **5 sessões simultâneas**
- Detecção de usuário já logado com opção de **forçar entrada**
- **Reconexão automática** ao recarregar a página (token salvo em `localStorage`)
- **Heartbeat** a cada 30 segundos para manter sessão ativa
- Expiração automática de sessões inativas (30 minutos)
- Ao sair, todos os bloqueios de campo do usuário são liberados automaticamente

---

### 📄 Estrutura da Ata

A ata é dividida em quatro blocos principais:

#### 🎵 Prelúdio / Abertura
| Campo | Descrição |
|---|---|
| Presidido | Quem preside a reunião |
| Dirigido | Quem dirige a reunião |
| Visitantes | Lista de nomes (até 10), com autocomplete |
| Anúncios e Reconhecimentos | Campo de texto livre |
| Hino de Abertura | Busca por número ou nome do hino |
| Oração de Abertura | Nome do irmão que ora |
| Regente | Nome do regente do coral |
| Pianista | Nome da pianista |

#### 📣 Assuntos da Ala e Estaca
Cada sub-bloco suporta múltiplas entradas com adição e remoção dinâmica de linhas:

| Sub-bloco | Campos por entrada |
|---|---|
| **Desobrigações** | Nome · Cargo — exibe instrução oficial para o condutor |
| **Apoios** | Nome · Cargo — exibe instrução oficial para o condutor |
| **Ordenações ao Sacerdócio Aarônico** | Nome · Ofício · Quem ordena |
| **Confirmações de Conversos** | Nome · Quem confirma |
| **Dar Nome e Abençoar Crianças** | Nome da criança · Quem abençoa *(1º domingo)* |

#### 🍞 Bênção e Distribuição do Sacramento
- Campo de **hino sacramental** com busca
- Orações do pão e da água exibidas em texto formatado para leitura durante a reunião

#### 🎶 Encerramento
| Campo | Descrição |
|---|---|
| Reunião de Jejum e Testemunho | Área de texto para registrar nomes *(1º domingo)* |
| Oradores (1º, 2º, 3º) | Nomes dos oradores com autocomplete |
| Hino Intermediário | Entre o 2º e 3º orador |
| Hino de Encerramento | Busca por número ou nome |
| Oração Final | Nome do irmão que ora |
| **Frequência** | Visitantes · Homens · Total |
| **Irmãos Faltantes** | Nome + Organização (lista dinâmica) |

---

### 🔄 Colaboração em Tempo Real

- **Polling automático** a cada 8 segundos: detecta alterações feitas por outros usuários e atualiza o formulário silenciosamente
- **Bloqueio de campos**: ao clicar em um campo, ele fica bloqueado para os demais usuários com indicador visual `✏️ [nome] editando...`
- Bloqueios expiram automaticamente após 30 segundos de inatividade no campo
- Notificação via **toast** quando a ata é atualizada por outro usuário

---

### 💾 Salvamento Automático

- Salva automaticamente **1,5 segundos** após qualquer alteração
- Indicador de status visível: `Salvando...` → `✅ Salvo` / `❌ Erro ao salvar`
- Controle de **conflito de versão**: nunca sobrescreve dados mais recentes sem verificar o timestamp

---

### 🔍 Autocomplete Inteligente

**Hinos:**
- Busca por número ou nome em uma lista completa do Hinário SUD
- Navegação por teclado (↑ ↓ Enter Esc)

**Membros:**
- Busca por qualquer parte do nome (mínimo 2 caracteres)
- Lista carregada do banco de dados (`membros_extras`)
- **Adição de novos membros** diretamente pelo campo, com persistência no banco
- Novos membros ficam disponíveis para todos os usuários imediatamente

---

### 📚 Diretório de Atas

- Painel lateral com **lista de todas as atas** cadastradas, ordenadas por data
- Busca por data ou nome do presidido
- Clique para trocar de ata instantaneamente sem sair do sistema

---

### 📊 Painel de Estatísticas

Relatório analítico gerado a partir do histórico de atas, com filtros por:
- **Período:** último mês · trimestre · semestre · 12 meses
- **Organização:** Quórum de Élderes · Sociedade de Socorro · Rapazes · Moças · JAS · Primária

**Métricas geradas:**
| Indicador | Descrição |
|---|---|
| Reuniões no período | Total de atas registradas |
| Frequência média | Média de presença por reunião |
| Ausências registradas | Total de entradas de faltantes |
| Média de faltantes/reunião | Razão entre ausências e reuniões |
| Irmãos críticos | Faltaram em mais de 70% das reuniões |
| Irmãos em rodízio | Faltaram entre 30% e 70% das reuniões |
| Frequência por mês | Tabela com média mensal e ausências |
| Ausências por organização | Distribuição com barra de progresso |
| Ranking de faltantes | Lista ordenada com total de faltas |

---

### 🖨️ Impressão Inteligente

Ao clicar em **Imprimir / PDF**, o sistema aplica uma lógica de economia de papel:

- **Bloco sem nenhum dado** → exibe apenas o cabeçalho + texto *"Sem dados"*
- **Bloco com dados parciais** → exibe apenas os sub-blocos e campos que foram preenchidos
- **Campos individuais vazios** são ocultados automaticamente
- Itens de frequência com valor zero são omitidos
- Após a impressão, o formulário retorna ao visual normal automaticamente

---

### 📋 Histórico de Alterações

- Cada campo alterado gera uma entrada no log com: **usuário**, **campo**, **valor anterior**, **valor novo** e **horário**
- Painel expansível no rodapé da ata com os últimos 100 registros

---

## 🗄️ Estrutura do Banco de Dados (Supabase)

O sistema utiliza as seguintes tabelas no Supabase:

### `atas`
Tabela principal com um registro por reunião dominical.

| Coluna | Tipo | Descrição |
|---|---|---|
| `id` | uuid | Chave primária |
| `data_reuniao` | date | Data do domingo (unique) |
| `presidido` | text | |
| `dirigido` | text | |
| `anuncios` | text | |
| `hino_abertura` | text | |
| `oracao_abertura` | text | |
| `regente` | text | |
| `pianista` | text | |
| `hino_sacramental` | text | |
| `hino_intermediario` | text | |
| `primeiro_orador` | text | |
| `segundo_orador` | text | |
| `terceiro_orador` | text | |
| `hino_encerramento` | text | |
| `oracao_encerramento` | text | |
| `testemunhos_jejum` | text | |
| `visitantes` | integer | Contagem numérica |
| `homens` | integer | Contagem numérica |
| `frequencia_total` | integer | |
| `visitantes_nomes` | jsonb | Array de strings com nomes |
| `faltantes_nomes` | jsonb | Array de objetos `{nome, org}` |
| `atualizada_por` | text | Último usuário a salvar |
| `atualizada_em` | timestamptz | Timestamp do último save |

---

### `desobrigacoes` / `apoios` / `ordenacoes` / `confirmacoes` / `bencaos_criancas`
Tabelas de entradas repetitivas vinculadas à ata.

| Coluna | Tipo | Descrição |
|---|---|---|
| `id` | uuid | Chave primária |
| `ata_id` | uuid | FK → `atas.id` |
| `nome` | text | Nome do membro |
| `cargo` / `oficio` | text | Cargo ou ofício (varia por tabela) |
| `por` | text | Quem realiza o ato (onde aplicável) |
| `ordem` | integer | Ordem de exibição |

---

### `membros_extras`
Lista de membros da ala para autocomplete.

| Coluna | Tipo |
|---|---|
| `id` | uuid |
| `nome` | text |
| `criado_por` | text |

---

### `sessoes_ativas`
Controle de usuários conectados.

| Coluna | Tipo |
|---|---|
| `id` | uuid |
| `usuario` | text |
| `token` | text |
| `ultima_atividade` | timestamptz |

---

### `campos_em_edicao`
Bloqueio de campos para edição colaborativa.

| Coluna | Tipo |
|---|---|
| `id` | uuid |
| `ata_id` | uuid |
| `campo_id` | text |
| `usuario` | text |
| `bloqueado_em` | timestamptz |

---

### `log_alteracoes`
Histórico de mudanças campo a campo.

| Coluna | Tipo |
|---|---|
| `id` | uuid |
| `ata_id` | uuid |
| `usuario` | text |
| `campo` | text |
| `valor_anterior` | text |
| `valor_novo` | text |
| `alterado_em` | timestamptz |

---

## ⚙️ Configuração e Deploy

### Pré-requisitos
- Conta no [Supabase](https://supabase.com) (plano gratuito é suficiente)
- As tabelas listadas acima criadas no banco
- Nenhum servidor necessário — o arquivo HTML roda diretamente no navegador

### Passo a passo

**1. Criar o projeto no Supabase**

Acesse [app.supabase.com](https://app.supabase.com), crie um novo projeto e anote:
- **Project URL** (ex: `https://xyzxyz.supabase.co`)
- **Anon/Public Key** (em Settings → API)

**2. Criar as tabelas**

Use o SQL Editor do Supabase para criar cada tabela descrita na seção anterior. Certifique-se de habilitar **Row Level Security (RLS)** com políticas permissivas para a chave anon, já que a autenticação é gerenciada pela própria aplicação.

**3. Configurar o arquivo HTML**

Abra o `ata_sacramental_v13.html` e localize o bloco de configuração no `<script>`:

```javascript
const SUPABASE_URL = 'https://SEU_PROJECT_ID.supabase.co';
const SUPABASE_ANON_KEY = 'SUA_ANON_KEY_AQUI';
```

Substitua pelos valores do seu projeto.

**4. Configurar os códigos de acesso**

Na função `fazerLogin()`, localize e substitua:

```javascript
if (codEstaca.replace(/\s/g,'') !== 'COD_ESTACA') { ... }
if (codAla.replace(/\s/g,'')    !== 'COD_ALA')    { ... }
```

Pelos códigos numéricos reais da sua Estaca e Ala.

**5. Popular membros**

Popule a tabela `membros_extras` com os nomes dos membros da ala diretamente pelo Supabase (Table Editor), ou deixe que o sistema adicione automaticamente conforme os usuários forem cadastrando.

**6. Abrir no navegador**

Basta abrir o arquivo `ata_sacramental_v13.html` diretamente no navegador. Não é necessário servidor local.

Para disponibilizar na rede da ala, hospede o arquivo em qualquer serviço estático gratuito:
- [GitHub Pages](https://pages.github.com)
- [Netlify](https://netlify.com)
- [Vercel](https://vercel.com)

---

## 🔒 Privacidade e Segurança

- **Nenhum dado pessoal** está incluído neste repositório
- A lista de membros é armazenada exclusivamente no banco Supabase, fora do código-fonte
- Os códigos de acesso (Estaca/Ala) funcionam como senha de entrada
- O token de sessão é gerado aleatoriamente e armazenado em `localStorage`
- Sessões expiram automaticamente após 30 minutos de inatividade

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia | Uso |
|---|---|
| HTML5 / CSS3 / JavaScript | Frontend — sem frameworks |
| [Supabase](https://supabase.com) | Banco de dados PostgreSQL + API REST |
| [Google Fonts](https://fonts.google.com) | Tipografia (EB Garamond + Inter) |

---

## 📁 Estrutura do Repositório

```
ata-sacramental/
├── ata_sacramental_v13.html   # Aplicação completa (único arquivo)
└── README.md                  # Este documento
```

---

## 🤝 Contribuições

Este projeto foi desenvolvido para uso interno da Ala Bougainville. Adaptações para outras alas e estacas são bem-vindas — basta ajustar os códigos de acesso, o nome da ala nos textos da interface e as credenciais do Supabase.

---

*Desenvolvido com dedicação para apoiar o trabalho dos líderes da Ala Bougainville.*
