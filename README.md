# digest-telegram

**Plano de consolidação de 21 agentes de digest e radar no motor `digest-kit`.**

Documento de planejamento, sem código ou bot. O épico inventaria 21 repositórios de coleta (RSS, scraping e APIs) com entrega no Telegram e define como cada um vira um tópico (`topics/*.ts`) ou provider (`providers/*.ts`) do [`juninmd/digest-kit`](https://github.com/juninmd/digest-kit).

## Contexto

Os agentes, na maioria, repetem Bun ou Node, TypeScript, SQLite, LiteLLM ou Gemini, Telegram e cron próprio. A manutenção é dispersa. A proposta é concentrar o motor no `digest-kit` e deixar em cada agente só fontes, prompt, filtros e formato.

## Inventário por tier

| Tier | Repositórios | Qtd. | Estratégia |
|---|---|---:|---|
| 1 | `ciencia-hoje`, `concursos-br`, `cursos-free`, `cve-radar`, `leis-radar`, `saude-diaria`, `vagas-dev` | 7 | Migração direta para `topics/<nome>.ts` |
| 2 | `release-radar`, `upstream-radar`, `steam-key-watcher`, `fast-news` | 4 | Feeds RSS + módulo de enriquecimento |
| 3 | `vagas-franca`, `role-radar`, `radar-ofertas`, `imoveis-franca` | 4 | Scrapers de DOM em `providers/` |
| 4 | `missas-franca`, `evangelho-do-dia`, `livro-do-dia`, `eleicoes-radar`, `dinheirama`, `worldcup-tracker` | 6 | Handlers de API como adapters |

<details>
<summary>Inventário dos 21 agentes</summary>

| Repositório | O que faz | Destino |
|---|---|---|
| `ciencia-hoje` | Ciência, IA e biotecnologia | Tópico |
| `concursos-br` | Editais e vagas | Tópico |
| `cursos-free` | Cursos gratuitos | Tópico |
| `cve-radar` | CVEs e exploits | Tópico + CVSS |
| `leis-radar` | Leis e regulamentação | Tópico |
| `saude-diaria` | Nutrição, treino e sono | Tópico |
| `vagas-dev` | Vagas remotas | Tópico |
| `release-radar` | Games, cinema e animes | Tópico + enriquecimento |
| `upstream-radar` | Versões e breaking changes | Provider npm |
| `steam-key-watcher` | Jogos grátis e promoções | Tópico + Reddit |
| `fast-news` | Notícias com RAG | Em avaliação |
| `vagas-franca` | Vagas em Franca/SP | Provider regional |
| `role-radar` | Eventos em Franca e Ribeirão | Provider regional |
| `radar-ofertas` | Promoções e preço | Provider de e-commerce |
| `imoveis-franca` | Imóveis em Franca/SP | Provider + alerta |
| `missas-franca` | Missas da Diocese de Franca | Provider regional |
| `evangelho-do-dia` | Evangelho e meditação | Tópico + TTS |
| `livro-do-dia` | Um livro por dia | Tópico |
| `eleicoes-radar` | Candidaturas das eleições 2026 | Adapter cívico |
| `dinheirama` | Pesquisa quantitativa B3 | Tópico de mercado |
| `worldcup-tracker` | Copa 2026 e placar | Adapter esportivo |

</details>

## O que cada tier exige

- **Tier 1:** digests RSS com SQLite e LLM. Mover URLs, prompts e filtros para o tópico e remover o código duplicado.
- **Tier 2:** enriquecimento próprio — categorias no `release-radar`, SemVer no `upstream-radar`, filtro de DLC no `steam-key-watcher`, embeddings no `fast-news`. Isso precisa existir no kit antes da migração.
- **Tier 3:** scrapers de DOM para 11 sites de emprego, mais de 15 ticketerias, e-commerces e mais de 100 imobiliárias. Cada fonte vira um provider com normalização de preço, empresa e data.
- **Tier 4:** APIs e processamento próprio (TSE, Brapi, liturgia, Google Books, FIFA Live). É um adapter por fonte, não um tópico de RSS.

## Decisões e pendências

- O motor compartilhado é o `digest-kit`; a manutenção passa a ser feita nele.
- Um repositório só é excluído depois da homologação da entrega unificada.
- `fast-news` tem duas saídas em aberto: manter o app web standalone ou migrar só o ingestor.
- `eleicoes-radar`, `dinheirama`, `worldcup-tracker` e `imoveis-franca` não têm exclusão prevista; entram como adapters ou tópicos.
- `role-radar` depende de eventos com data futura no scheduler; `worldcup-tracker`, de eventos esportivos ao vivo.
- O critério de homologação por tópico não está definido.

## Status

O inventário está concluído neste documento. Nenhuma migração foi marcada como concluída, e os quatro sprints — Tier 1 em `topics/`, scrapers regionais, releases/entretenimento e exclusão dos repositórios antigos — seguem pendentes.

## Limitações

- O documento não define prazos, responsáveis nem critérios de aceite por tópico.
- Os rankings e números do inventário são retratos do momento em que o épico foi escrito.
- Não há arquivo de licença no repositório.
