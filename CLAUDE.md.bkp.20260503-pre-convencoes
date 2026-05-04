# CLAUDE.md — Blog-Cripto SAFIE

## O que é este projeto
Blog automatizado em HTML estático, publicado em **cripto.safie.blog.br**, com artigos gerados diariamente via Claude API.
O blog cobre direito e contabilidade aplicados a criptoativos e blockchain, com foco no mercado brasileiro.

## ATENÇÃO: dois domínios completamente diferentes

| Domínio | O que é | Pode alterar? |
|---|---|---|
| safie.com.br | Site institucional da SAFIE | **NUNCA** |
| safie.blog.br | Rede de blogs temáticos | Sim, é este projeto |
| cripto.safie.blog.br | Este blog específico | Sim |

**NUNCA modifique, acesse para edição ou mencione safie.com.br como destino de qualquer ação de código.**
safie.com.br é acessado APENAS para extrair identidade visual (cores, fontes) na Fase 2.

## Estrutura de pastas

```
Blog-Cripto/
├── config/          # Configurações do blog (blog.json, temas.json, fontes.json)
├── dados/           # Histórico de notícias e controle de consumo Apify
├── templates/       # Templates HTML (index, artigo, tema, sobre)
├── assets/
│   ├── css/         # Estilos
│   ├── js/          # Scripts (busca, paginação)
│   └── img/         # Imagens e ícones
├── artigos/         # HTMLs gerados de cada artigo
├── temas/           # Páginas de listagem por tema
├── scripts/         # Scripts Python do pipeline
│   ├── buscar_noticia.py
│   ├── gerar_artigo.py
│   ├── otimizar_seo.py
│   └── publicar.py
├── logs/            # Logs diários (não versionados)
├── rodar_diario.sh  # Script orquestrador (chamado pelo launchd às 7h)
├── sitemap.xml      # Atualizado automaticamente a cada publicação
├── robots.txt
├── .env             # Credenciais (NÃO versionado)
└── .env.template    # Modelo de credenciais (versionado, sem valores reais)
```

## Credenciais necessárias (arquivo .env)
- `APIFY_TOKEN` — busca de notícias via Google News Scraper
- `ANTHROPIC_API_KEY` — geração de artigos via Claude API
- `GITHUB_TOKEN` — push automático dos artigos
- `GITHUB_REPO` — repositório no formato `usuario/nome-repo`

**Nunca hardcode credenciais. Sempre ler de variável de ambiente.**

## Pipeline diário (rodar_diario.sh — executa às 7h via launchd)
1. `buscar_noticia.py` — busca notícias via Apify (fallback: RSS)
2. `gerar_artigo.py` — gera artigo via Claude API
3. `otimizar_seo.py` — aplica tags SEO e schema.org
4. `publicar.py` — gera HTML, atualiza home/sitemap, commit + push GitHub

## Regras de SEO e GEO
- Título: máximo 60 caracteres, com palavra-chave principal
- Meta description: máximo 155 caracteres
- Estrutura obrigatória: resumo executivo → contexto jurídico → impacto prático → FAQ (3-5 perguntas)
- Schema.org: BlogPosting + FAQPage em JSON-LD
- URL: `https://cripto.safie.blog.br/artigos/AAAA-MM-DD-slug-do-artigo`
- Artigos: mínimo 800, máximo 1.500 palavras
- Tom: técnico, direto, sem juridiquês, sem clichês

## Regras de consumo Apify
- Registrar cada chamada em `dados/consumo_apify.json`
- Se erro de limite esgotado → cair automaticamente para RSS
- Se RSS também falhar → usar tema evergreen da lista

## Replicabilidade
Este projeto é o template para todos os blogs de safie.blog.br.
Toda configuração específica de nicho está em `config/`. Nenhum texto de "cripto" deve aparecer hardcoded nos scripts ou templates.
Ver `REPLICAR.md` para o processo completo.

## Estado atual do projeto (2026-04-28)
- **Fase 1 concluída:** Estrutura de pastas, configs, Git
- **Fase 2 concluída:** Interface HTML/CSS (identidade SAFIE), busca em JS
- **Fase 3 concluída:** buscar_noticia.py — RSS funcionando, Apify opcional via config
- **Fase 4 concluída:** gerar_artigo.py + otimizar_seo.py + publicar.py + rodar_diario.sh — pipeline em 4 etapas
- **Fase 5 concluída:**
  - GitHub: https://github.com/lucasm-mantovani/safie-blog-cripto
  - Cloudflare Pages: https://safie-blog-cripto.pages.dev (no ar)
  - Domínio: cripto.safie.blog.br (DNS propagado em 2026-04-28)
  - Cron job (launchd): ativo, roda todo dia às 7h
- **Fase 6 concluída (2026-04-28):**
  - DNS propagado e HTTP 200 confirmados
  - robots.txt + sitemap.xml funcionando
  - Schema.org BlogPosting + FAQPage em todos os artigos
  - meta robots, keywords, og:*, twitter:* no template
  - REPLICAR.md finalizado com checklist da Fase 6
  - Validação manual pendente: Google Rich Results Test + PageSpeed Insights (opcional, após próximo artigo gerado)
