# [EPIC] Unificação e Padronização de 21 Agentes e Radars no `digest-kit`

Este épico consolida o inventário técnico de todos os **21 repositórios** que executam algum tipo de coleta (RSS, scraping, APIs) com entrega de digests ou alertas no **Telegram**.

O objetivo deste backlog é unificar a manutenção dispersa em torno do motor compartilhado [`juninmd/digest-kit`](https://github.com/juninmd/digest-kit), convertendo os repositórios isolados em arquivos de tópicos (`topics/*.ts`) ou plugins de extração (`providers/*.ts`).

---

## 📋 Matriz Geral de Migração

| Grupo | Repositórios | Complexidade de Migração | Estratégia no digest-kit |
|---|---|---|---|
| **Tier 1: Digests RSS Padronizados** | `ciencia-hoje`, `concursos-br`, `cursos-free`, `cve-radar`, `leis-radar`, `saude-diaria`, `vagas-dev` | Baixa (Direta) | Converter direto em arquivos de configuração de tópicos `topics/<nome>.ts`. |
| **Tier 2: Notícias & Releases Pop** | `release-radar`, `upstream-radar`, `steam-key-watcher`, `fast-news` | Média | Incorporar feeds RSS no motor e plugar módulo de enriquecimento. |
| **Tier 3: Crawlers Regionais & E-commerce** | `vagas-franca`, `role-radar`, `radar-ofertas`, `imoveis-franca` | Média/Alta | Extrair scrapers de DOM para `providers/` reutilizáveis. |
| **Tier 4: Fé, Dados Abertos & Finanças** | `missas-franca`, `evangelho-do-dia`, `livro-do-dia`, `eleicoes-radar`, `dinheirama`, `worldcup-tracker` | Média | Migrar handlers de APIs para adapters do kit. |

---

## 🔍 Detalhamento Repositório a Repositório

### 1. `ciencia-hoje`
* **Repositório:** [`juninmd/ciencia-hoje`](https://github.com/juninmd/ciencia-hoje)
* **O que faz:** Digest diário matinal com as principais descobertas científicas, astronomia, IA e biotecnologia.
* **Stack:** Bun, TypeScript, `bun:sqlite`, LiteLLM / Gemini, Telegram Bot API.
* **Providers (Fontes):**
  - ScienceDaily Top Science (`https://www.sciencedaily.com/rss/top/science.xml`)
  - Nature Journal (`https://www.nature.com/nature.rss`)
  - arXiv Computer Science / AI (`http://export.arxiv.org/rss/cs`)
* **O que tem de customizado:**
  - Prompt com filtro anti-sensacionalismo e explicação "por que isso importa".
* **Backlog para padronizar no digest-kit:**
  - [ ] Mover URLs para `topics/ciencia-hoje.ts`.
  - [ ] Portar prompt customizado de síntese.
  - [ ] Testar disparo com `bun run digest ciencia-hoje`.
  - [ ] Deletar repositório `juninmd/ciencia-hoje`.

---

### 2. `concursos-br`
* **Repositório:** [`juninmd/concursos-br`](https://github.com/juninmd/concursos-br)
* **O que faz:** Digest diário de editais abertos, prazos de inscrição e vagas de concursos públicos no Brasil.
* **Stack:** Bun, TypeScript, `bun:sqlite`, Google News RSS, LiteLLM.
* **Providers (Fontes):**
  - Google News RSS: `concurso público edital inscrições abertas` (pt-BR)
  - Google News RSS: `edital concurso 2026 vagas` (pt-BR)
* **O que tem de customizado:**
  - Parser de relevância regional e filtro de escolaridade/salário.
* **Backlog para padronizar no digest-kit:**
  - [ ] Adicionar Google News feed parser em `topics/concursos-br.ts`.
  - [ ] Padronizar prompt de formatação com emojis de status (🚨 Inscrições, 📅 Provas).
  - [ ] Deletar repositório `juninmd/concursos-br`.

---

### 3. `cursos-free`
* **Repositório:** [`juninmd/cursos-free`](https://github.com/juninmd/cursos-free)
* **O que faz:** Monitora cursos gratuitos de tecnologia com certificação reconhecida e bolsas abertas.
* **Stack:** Bun, TypeScript, `bun:sqlite`, RSS parser, LiteLLM.
* **Providers (Fontes):**
  - freeCodeCamp News (`https://www.freecodecamp.org/news/rss/`)
  - Google News RSS: `curso gratuito certificação tecnologia inscrições` (pt-BR)
* **O que tem de customizado:**
  - Deduplicação por hash de título normalizado para evitar cursos requentados.
* **Backlog para padronizar no digest-kit:**
  - [ ] Criar `topics/cursos-free.ts`.
  - [ ] Deletar repositório `juninmd/cursos-free`.

---

### 4. `cve-radar`
* **Repositório:** [`juninmd/cve-radar`](https://github.com/juninmd/cve-radar)
* **O que faz:** Digest de cibersegurança focado em vulnerabilidades 0-day, avisos CISA e exploits ativos.
* **Stack:** Bun, TypeScript, `bun:sqlite`, RSS parser, Telegram API.
* **Providers (Fontes):**
  - CISA Advisories (`https://www.cisa.gov/cybersecurity-advisories/all.xml`)
  - The Hacker News (`https://feeds.feedburner.com/TheHackersNews`)
  - BleepingComputer (`https://www.bleepingcomputer.com/feed/`)
* **O que tem de customizado:**
  - Extração de scores CVSS e tag de criticidade (🔴 Crítico, 🟡 Médio).
* **Backlog para padronizar no digest-kit:**
  - [ ] Adicionar parser de regex CVSS no hook de pós-processamento do `digest-kit`.
  - [ ] Mover feeds para `topics/cve-radar.ts`.
  - [ ] Deletar repositório `juninmd/cve-radar`.

---

### 5. `leis-radar`
* **Repositório:** [`juninmd/leis-radar`](https://github.com/juninmd/leis-radar)
* **O que faz:** Resumo de novas leis sancionadas, medidas provisórias e marcos regulatórios no Brasil.
* **Stack:** Bun, TypeScript, `bun:sqlite`, Google News RSS, LiteLLM.
* **Providers (Fontes):**
  - Google News RSS: `nova lei decreto sancionado` (pt-BR)
  - Google News RSS: `regulamentação regra norma governo federal` (pt-BR)
* **O que tem de customizado:**
  - Prompt jurídico simplificado que traduz juridiquês para linguagem clara.
* **Backlog para padronizar no digest-kit:**
  - [ ] Configurar `topics/leis-radar.ts`.
  - [ ] Deletar repositório `juninmd/leis-radar`.

---

### 6. `saude-diaria`
* **Repositório:** [`juninmd/saude-diaria`](https://github.com/juninmd/saude-diaria)
* **O que faz:** Digest diário com artigos científicos sobre nutrição, longevidade, treino e sono.
* **Stack:** Bun, TypeScript, `bun:sqlite`, ScienceDaily RSS.
* **Providers (Fontes):**
  - ScienceDaily Health & Medicine (`https://www.sciencedaily.com/rss/health_medicine.xml`)
  - ScienceDaily Nutrition (`https://www.sciencedaily.com/rss/health_medicine/nutrition.xml`)
  - ScienceDaily Fitness (`https://www.sciencedaily.com/rss/health_medicine/fitness.xml`)
* **O que tem de customizado:**
  - Formato de pílulas práticas ("O que a ciência descobriu hoje").
* **Backlog para padronizar no digest-kit:**
  - [ ] Criar `topics/saude-diaria.ts`.
  - [ ] Deletar repositório `juninmd/saude-diaria`.

---

### 7. `vagas-dev`
* **Repositório:** [`juninmd/vagas-dev`](https://github.com/juninmd/vagas-dev)
* **O que faz:** Digest de vagas remotas de desenvolvimento de software em empresas globais (USD/EUR).
* **Stack:** Bun, TypeScript, `bun:sqlite`, RemoteOK RSS, WWR RSS.
* **Providers (Fontes):**
  - RemoteOK (`https://remoteok.com/remote-dev-jobs.rss`)
  - WeWorkRemotely Dev (`https://weworkremotely.com/categories/remote-programming-jobs.rss`)
  - WeWorkRemotely Fullstack (`https://weworkremotely.com/categories/remote-full-stack-programming-jobs.rss`)
* **O que tem de customizado:**
  - Filtro automático descartando vagas híbridas/on-site e tags de faixa salarial.
* **Backlog para padronizar no digest-kit:**
  - [ ] Criar `topics/vagas-dev.ts`.
  - [ ] Deletar repositório `juninmd/vagas-dev`.

---

### 8. `vagas-franca`
* **Repositório:** [`juninmd/vagas-franca`](https://github.com/juninmd/vagas-franca)
* **O que faz:** Monitora todas as vagas de emprego, estágios e oportunidades na cidade de Franca/SP.
* **Stack:** Bun, TypeScript, Cheerio / Fetch nativo, SQLite, Telegram Bot.
* **Providers (Fontes - 11 sites locais):**
  - ACIF Franca (`acif.org.br`)
  - BNE Franca (`bne.com.br`)
  - Emprego em Franca (`empregoemfranca.com.br`)
  - Emprego Franca (`empregofranca.com.br`)
  - Empregos.com.br
  - Franca Classificados
  - Gupy (empresas com unidade em Franca)
  - Hard Franca, InfoJobs Franca, Melhores Empregos, Trabalha Brasil
* **O que tem de customizado:**
  - 11 scrapers customizados em `src/sources/*.ts` com normalização de salários e empresas.
* **Backlog para padronizar no digest-kit:**
  - [ ] Criar diretório `providers/vagas-franca/` no `digest-kit` com os 11 scrapers como provider dinâmico.
  - [ ] Integrar no cron diário.
  - [ ] Deletar repositório `juninmd/vagas-franca`.

---

### 9. `role-radar`
* **Repositório:** [`juninmd/role-radar`](https://github.com/juninmd/role-radar)
* **O que faz:** Radar de lazer em Franca-SP e Ribeirão Preto-SP: shows, teatro, cinema, basquete, festas e festivais.
* **Stack:** Bun, TypeScript, Linkedom, SQLite, Telegram Bot.
* **Providers (Fontes - 15+ plataformas):**
  - Sympla, Bilheteria Digital, Blueticket, Guichê Web
  - Ingresso.com (Cinema Franca Shopping)
  - Q2 Ingressos, Ticket360, Zig Tickets, Uhuu, Mega Bilheteria
  - Theatro Pedro II, Sesc Ribeirão, Sesi SP, Basquete Franca
* **O que tem de customizado:**
  - Módulos de parsing de DOM para 15 ticketerias diferentes e extração de faixa de preço.
* **Backlog para padronizar no digest-kit:**
  - [ ] Portar a pasta `src/sources/` para `providers/role-radar/` dentro do monorepo.
  - [ ] Adicionar suporte a eventos com data futura no scheduler do `digest-kit`.
  - [ ] Deletar repositório `juninmd/role-radar`.

---

### 10. `radar-ofertas`
* **Repositório:** [`juninmd/radar-ofertas`](https://github.com/juninmd/radar-ofertas)
* **O que faz:** Monitoramento contínuo de promoções, cupons e quedas de preço em e-commerces.
* **Stack:** Bun, TypeScript, SQLite, LiteLLM, Telegram Bot.
* **Providers (Fontes):**
  - Mercado Livre (busca e tracking de itens)
  - Amazon Brasil (scraping de ASIN e buscas)
  - Shopee Brasil (API interna de busca de produtos)
  - Magazine Luiza
* **O que tem de customizado:**
  - Histórico de preço em SQLite para verificar se a promoção é real (evitar "metade do dobro").
  - Sistema de feedback de recomendação via bot.
* **Backlog para padronizar no digest-kit:**
  - [ ] Portar módulos de scraping de e-commerce como `providers/e-commerce/`.
  - [ ] Deletar repositório `juninmd/radar-ofertas`.

---

### 11. `fast-news`
* **Repositório:** [`juninmd/fast-news`](https://github.com/juninmd/fast-news)
* **O que faz:** Agregador e inteligência de notícias em tempo real com broadcast no Telegram e web.
* **Stack:** Node 24, TypeScript, Express, Bull Queue, Redis, Postgres/SQLite, Telegraf, RAG / Embeddings.
* **Providers (Fontes):**
  - RSS: G1, Folha, Estadão, UOL, TechCrunch, The Verge, Ars Technica, Hacker News, BBC, Reuters, CNN
  - Twitter / X Trends (tópicos mais comentados)
  - YouTube Trending API (vídeos em alta)
* **O que tem de customizado:**
  - Deduplicação de matérias cruzadas de múltiplos jornais com embeddings/similaridade semântica.
  - Avaliação de credibilidade de fonte.
* **Backlog para padronizar no digest-kit:**
  - [ ] Extrair a lista unificada de feeds RSS para `topics/noticias-gerais.ts`.
  - [ ] Avaliar se o motor completo do fast-news continua como app web standalone ou se o ingestor migra para o kit.

---

### 12. `release-radar`
* **Repositório:** [`juninmd/release-radar`](https://github.com/juninmd/release-radar)
* **O que faz:** Notifica lançamentos oficiais e datas de estreias no mundo dos games, cinema e animes.
* **Stack:** Bun, TypeScript, RSS, Telegram Bot.
* **Providers (Fontes):**
  - PlayStation Blog (`https://blog.playstation.com/feed/`)
  - Xbox Wire (`https://news.xbox.com/en-us/feed/`)
  - Gematsu (`https://www.gematsu.com/feed`)
  - Anime News Network (`https://www.animenewsnetwork.com/all/rss.xml`)
  - SlashFilm & Variety & What's on Netflix
* **O que tem de customizado:**
  - Agrupamento por categoria (🎮 Games, 🍿 Filmes, ⛩️ Animes).
* **Backlog para padronizar no digest-kit:**
  - [ ] Configurar `topics/release-radar.ts`.
  - [ ] Deletar repositório `juninmd/release-radar`.

---

### 13. `upstream-radar`
* **Repositório:** [`juninmd/upstream-radar`](https://github.com/juninmd/upstream-radar)
* **O que faz:** Monitora o ecossistema open-source e notifica novas versões e breaking changes de libs usadas no portfólio.
* **Stack:** Bun, TypeScript, npm Registry API, Telegram Bot.
* **Providers (Fontes):**
  - npm Registry API (`registry.npmjs.org`)
  - Bun Releases (`bun.sh`)
  - GitHub Releases de pacotes
* **O que tem de customizado:**
  - Comparação SemVer e destaque de breaking changes.
* **Backlog para padronizar no digest-kit:**
  - [ ] Criar plugin `providers/npm-registry.ts` no `digest-kit`.
  - [ ] Deletar repositório `juninmd/upstream-radar`.

---

### 14. `steam-key-watcher`
* **Repositório:** [`juninmd/steam-key-watcher`](https://github.com/juninmd/steam-key-watcher)
* **O que faz:** Alerta imediato no Telegram de jogos 100% grátis (Steam, Epic Games, GOG) antes que a promoção expire.
* **Stack:** Node/Bun, TypeScript, Reddit API / RSS, Telegram Bot.
* **Providers (Fontes):**
  - Reddit r/FreeGameFindings
  - Reddit r/GameDeals
  - GrabFreeGames RSS
  - Canais de avisos do Telegram
* **O que tem de customizado:**
  - Filtro para ignorar DLCs/betas e alertar apenas jogos base completos que ficam permanentemente na conta.
* **Backlog para padronizar no digest-kit:**
  - [ ] Adicionar feed Reddit no `topics/jogos-gratis.ts`.
  - [ ] Deletar repositório `juninmd/steam-key-watcher`.

---

### 15. `eleicoes-radar`
* **Repositório:** [`juninmd/eleicoes-radar`](https://github.com/juninmd/eleicoes-radar)
* **O que faz:** Monitoramento técnico e comparador de candidaturas políticas das eleições 2026.
* **Stack:** Bun, TypeScript, Web Frontend, SQLite, Telegram Bot.
* **Providers (Fontes):**
  - TSE DivulgaCandContas (Dados abertos oficiais)
  - API Dados Abertos da Câmara dos Deputados
  - API do Senado Federal
  - Consulta CNPJ Receita Federal
* **O que tem de customizado:**
  - Cruzamento de dados de mais de 20 mil candidaturas sem necessidade de chave de API.
* **Backlog para padronizar no digest-kit:**
  - [ ] Manter os scrapers do TSE como adapter de dados cívicos.

---

### 16. `missas-franca`
* **Repositório:** [`juninmd/missas-franca`](https://github.com/juninmd/missas-franca)
* **O que faz:** Bot de Telegram interativo e digest de horários de missas, confissões e capelas de todas as paróquias de Franca/SP.
* **Stack:** Bun, TypeScript, Telegraf / Telegram Bot API, SQLite.
* **Providers (Fontes):**
  - Portal Oficial da Diocese de Franca (`diocesefranca.org.br`)
* **O que tem de customizado:**
  - Scraper estruturado que extrai horários, pároco, telefone, endereço, geolocalização e fotos das igrejas.
* **Backlog para padronizar no digest-kit:**
  - [ ] Portar o scraper para `providers/diocese-franca.ts`.
  - [ ] Deletar repositório `juninmd/missas-franca`.

---

### 17. `evangelho-do-dia`
* **Repositório:** [`juninmd/evangelho-do-dia`](https://github.com/juninmd/evangelho-do-dia)
* **O que faz:** Leitura do Evangelho diário, áudio de narração, meditação e propósito do dia.
* **Stack:** TypeScript, LiteLLM, Telegram Bot, Web PWA.
* **Providers (Fontes):**
  - API Canção Nova / Liturgia Diária da CNBB
* **O que tem de customizado:**
  - Geração de meditação em áudio (TTS) e reflexão diária.
* **Backlog para padronizar no digest-kit:**
  - [ ] Configurar `topics/evangelho-do-dia.ts` no `digest-kit`.
  - [ ] Deletar repositório `juninmd/evangelho-do-dia`.

---

### 18. `livro-do-dia`
* **Repositório:** [`juninmd/livro-do-dia`](https://github.com/juninmd/livro-do-dia)
* **O que faz:** Indica e resume um livro por dia no Telegram (foco em negócios, hábitos, disciplina e mentalidade).
* **Stack:** Bun, TypeScript, SQLite, LiteLLM, Telegram Bot.
* **Providers (Fontes):**
  - Google Books API
  - Open Library API
  - Amazon Associates
* **O que tem de customizado:**
  - Prompt com resumo estruturado em tópicos ("3 principais lições") e link afiliado.
* **Backlog para padronizar no digest-kit:**
  - [ ] Configurar `topics/livro-do-dia.ts`.
  - [ ] Deletar repositório `juninmd/livro-do-dia`.

---

### 19. `dinheirama`
* **Repositório:** [`juninmd/dinheirama`](https://github.com/juninmd/dinheirama)
* **O que faz:** Pesquisa quantitativa para ações da B3: simulações de Monte Carlo, dividendos, múltiplos e relatórios diários.
* **Stack:** Bun/Node, TypeScript, Brapi API, Python (modelos quant), Telegram Bot.
* **Providers (Fontes):**
  - Brapi API (`brapi.dev`)
  - Status Invest / Fundamentus
  - Fast-news financial feed
* **O que tem de customizado:**
  - Pipeline matemático de simulação de risco e alertas de valuation no Telegram.
* **Backlog para padronizar no digest-kit:**
  - [ ] Conectar os alertas diários de fechamento de mercado como tópico no `digest-kit`.

---

### 20. `worldcup-tracker`
* **Repositório:** [`juninmd/worldcup-tracker`](https://github.com/juninmd/worldcup-tracker)
* **O que faz:** Monitoramento em tempo real dos jogos da Copa do Mundo 2026 com probabilidade estatística de placar (Poisson/Elo) e alertas de gol.
* **Stack:** Bun, TypeScript, FIFA Live API, Telegram Bot.
* **Providers (Fontes):**
  - FIFA Official Live Match API
* **O que tem de customizado:**
  - Algoritmo preditivo de placar e webhook de lances em tempo real.
* **Backlog para padronizar no digest-kit:**
  - [ ] Adapter para eventos de esportes ao vivo.

---

### 21. `imoveis-franca`
* **Repositório:** [`juninmd/imoveis-franca`](https://github.com/juninmd/imoveis-franca)
* **O que faz:** Scraper contínuo de mais de 100 imobiliárias de Franca/SP e portais nacionais para identificar oportunidades e médias de metro quadrado.
* **Stack:** TypeScript, Cheerio, Redis, Telegram Bot.
* **Providers (Fontes - 100+ imobiliárias):**
  - OLX, VivaReal, ZapImóveis, Imovelweb
  - Imobiliárias locais: Agnello, Parra, Casabella, Novaes, Lemos, Zago, Cintra, etc.
* **O que tem de customizado:**
  - Camada de abstração para 10 plataformas de imobiliárias (Kenlo, Casasoft, Navent, etc.).
* **Backlog para padronizar no digest-kit:**
  - [ ] Migrar alertas de "novo imóvel abaixo do preço de mercado" para o Telegram através de hook do kit.

---

## 🎯 Próximos Passos de Execução

1. [ ] **Sprint 1:** Migrar os 7 digests do Tier 1 (`ciencia-hoje`, `concursos-br`, `cursos-free`, `cve-radar`, `leis-radar`, `saude-diaria`, `vagas-dev`) diretamente para `topics/` no `digest-kit`.
2. [ ] **Sprint 2:** Criar suíte de scrapers regionais (`vagas-franca`, `role-radar`, `missas-franca`) sob a pasta `providers/regional-franca/`.
3. [ ] **Sprint 3:** Migrar digests de releases e entretenimento (`release-radar`, `steam-key-watcher`, `livro-do-dia`).
4. [ ] **Sprint 4:** Excluir os repositórios individuais obsoletos do GitHub após homologar a entrega unificada.
