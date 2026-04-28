# Como replicar este blog para outros subdomínios de safie.blog.br

Este guia permite criar um novo blog temático (ex: `trabalhista.safie.blog.br`) a partir deste projeto.

---

## 1. Clonar a pasta do projeto

No terminal, dentro de `~/CLAUDE/`:

```bash
cp -r Blog-Cripto Blog-Trabalhista
cd Blog-Trabalhista
```

---

## 2. Ajustar as configurações de nicho

Edite os três arquivos em `config/`:

### config/blog.json
Altere todos os campos específicos do nicho:
```json
{
  "nome": "SAFIE Trabalhista",
  "descricao": "Direito trabalhista e previdenciário para empresas",
  "dominio": "trabalhista.safie.blog.br",
  "url_completa": "https://trabalhista.safie.blog.br",
  ...
}
```

### config/temas.json
Substitua os temas e palavras-chave pelo novo nicho.

### config/fontes.json
Substitua os feeds RSS por fontes relevantes para o novo nicho.

---

## 3. Limpar dados do blog anterior

```bash
# Zerar histórico de notícias
echo '{"noticias":[]}' > dados/historico_noticias.json

# Zerar consumo Apify
echo '{"registros":[]}' > dados/consumo_apify.json

# Remover artigos do blog anterior
rm -f artigos/*.html temas/*.html
```

---

## 4. Criar repositório no GitHub

1. Acesse github.com → botão "New repository"
2. Nome sugerido: `safie-blog-trabalhista` (ou o nicho correspondente)
3. Visibilidade: **Público** (necessário para Cloudflare Pages gratuito)
4. Não inicializar com README (já temos arquivos)
5. Copie a URL do repositório (ex: `https://github.com/usuario/safie-blog-trabalhista.git`)

No terminal, dentro da pasta do novo blog:
```bash
git init
git add .
git commit -m "setup: estrutura inicial blog trabalhista"
git remote add origin https://github.com/usuario/safie-blog-trabalhista.git
git push -u origin main
```

Atualize o `.env`:
```
GITHUB_REPO=usuario/safie-blog-trabalhista
```

---

## 5. Criar projeto no Cloudflare Pages

1. Acesse [dash.cloudflare.com](https://dash.cloudflare.com)
2. Vá em **Pages → Criar uma aplicação → Conectar ao Git**
3. Selecione o repositório recém-criado (`safie-blog-trabalhista`)
4. Configurações de build:
   - **Framework preset:** None (site estático puro)
   - **Build command:** (deixar em branco)
   - **Output directory:** `/` (raiz do projeto)
5. Clique em **Save and Deploy**
6. Aguarde o deploy concluir
7. Anote o domínio gerado pelo Cloudflare (ex: `safie-blog-trabalhista.pages.dev`)

---

## 6. Registrar safie.blog.br no Registro.br (apenas uma vez)

> Se já tiver registrado safie.blog.br para o blog de cripto, pule este passo.

1. Acesse [registro.br](https://registro.br)
2. Pesquise `safie.blog.br`
3. Registre em nome de pessoa física (CPF) ou jurídica (CNPJ)
4. Complete o pagamento (custo anual ~R$ 40)

---

## 7. Criar subdomínio no Registro.br

1. Acesse [registro.br](https://registro.br) → Seus domínios → `safie.blog.br`
2. Vá em **DNS → Gerenciar zona DNS**
3. Adicione um registro:
   - **Tipo:** CNAME
   - **Nome:** `trabalhista` (apenas o subdomínio, sem o domínio base)
   - **Destino:** `safie-blog-trabalhista.pages.dev` (endereço do Cloudflare Pages)
   - **TTL:** 3600 (ou automático)
4. Salve e aguarde propagação (pode levar até 24h, normalmente menos)

> **Alternativa — usar nameservers do Cloudflare:**
> Se preferir gerenciar DNS diretamente no Cloudflare (recomendado para mais controle):
> 1. No Cloudflare, adicione o domínio `safie.blog.br` como zona
> 2. O Cloudflare fornecerá dois nameservers (ex: `ns1.cloudflare.com`)
> 3. No Registro.br, atualize os nameservers do domínio para os fornecidos pelo Cloudflare
> 4. A partir daí, todos os subdomínios são gerenciados no painel do Cloudflare

---

## 8. Configurar domínio personalizado no Cloudflare Pages

1. No painel do Cloudflare Pages → projeto do blog → **Domínios personalizados**
2. Clique em **Configurar um domínio personalizado**
3. Digite `trabalhista.safie.blog.br`
4. O Cloudflare verificará e ativará o domínio automaticamente (se os nameservers forem do Cloudflare) ou fornecerá instruções para o CNAME

---

## 9. Configurar cron job no macOS (launchd)

Crie um arquivo de configuração do launchd:

```bash
# Substitua "trabalhista" pelo nome do blog
LABEL="br.safie.blog.trabalhista.diario"
PASTA="/Users/lucasm.mantovani/CLAUDE/Blog-Trabalhista"

cat > ~/Library/LaunchAgents/${LABEL}.plist << EOF
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>${LABEL}</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/zsh</string>
        <string>${PASTA}/rodar_diario.sh</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>7</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>${PASTA}/logs/launchd.log</string>
    <key>StandardErrorPath</key>
    <string>${PASTA}/logs/launchd_erro.log</string>
</dict>
</plist>
EOF

launchctl load ~/Library/LaunchAgents/${LABEL}.plist
```

---

## 10. Validação SEO (Fase 6) — após DNS propagar

Após o DNS do novo subdomínio propagar (normalmente em menos de 24h), valide:

**Verificações automáticas (terminal):**
```bash
# DNS propagado?
dig +short CNAME novo.safie.blog.br

# Site respondendo?
curl -s -o /dev/null -w "%{http_code}" https://novo.safie.blog.br

# Robots.txt e sitemap?
curl -s https://novo.safie.blog.br/robots.txt
curl -s https://novo.safie.blog.br/sitemap.xml | grep -c "<url>"
```

**Verificações manuais (browser):**
1. **Google Rich Results Test** — acesse https://search.google.com/test/rich-results e cole a URL de um artigo. Deve mostrar BlogPosting + FAQPage sem erros.
2. **PageSpeed Insights** — acesse https://pagespeed.web.dev e teste o domínio do blog. Meta: SEO ≥ 90.

**O que já vem configurado automaticamente:**
- Schema.org BlogPosting + FAQPage em cada artigo (gerado por `gerar_artigo.py`)
- meta robots, description, keywords, og:* e twitter:* no template
- sitemap.xml atualizado a cada artigo publicado
- robots.txt apontando para o sitemap

---

## Checklist de replicação

- [ ] Pasta copiada de `Blog-Cripto`
- [ ] `config/blog.json` atualizado com nome, domínio e descrição do novo nicho
- [ ] `config/temas.json` atualizado com temas e palavras-chave do novo nicho
- [ ] `config/fontes.json` atualizado com feeds RSS do novo nicho
- [ ] Dados zerados (`historico_noticias.json` e `consumo_apify.json`)
- [ ] Artigos e páginas de tema anteriores removidos
- [ ] `.env` atualizado com `GITHUB_REPO` correto
- [ ] Repositório GitHub criado e código enviado
- [ ] Projeto no Cloudflare Pages criado e conectado ao repositório
- [ ] Registro CNAME criado no Registro.br (ou Cloudflare DNS)
- [ ] Domínio personalizado configurado no Cloudflare Pages
- [ ] Cron job (launchd) configurado para o novo blog
- [ ] Primeiro artigo gerado e publicado com sucesso
- [ ] DNS propagado e HTTP 200 confirmados
- [ ] Google Rich Results Test sem erros (BlogPosting + FAQPage)
- [ ] PageSpeed Insights SEO ≥ 90
