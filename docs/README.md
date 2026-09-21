# Cópia local — Corporal Class Black (black-friday-2026)

Cópia fiel da página `https://corporalacademy.com.br/black-friday-2026`
(site Framer, capturado em 14/09/2026, publicação Framer de 14/09/2026 14:37 UTC).

## Como rodar

```bash
python3 -m http.server 4188 --directory site
```

Abra `http://localhost:4188`. Precisa ser servida por HTTP — abrir o
`index.html` via `file://` quebra os módulos ES (`.mjs`).

## O que está local

- `index.html` — HTML renderizado no servidor (SSR), com CSS inline do Framer
- `assets/framer/sites/…` — 16 módulos `.mjs` (React, motion, smooth scroll,
  componentes da página, formulário). Os imports entre eles são relativos,
  então a árvore funciona offline.
- `assets/framer/assets/` — 50 fontes `.woff2`
- `assets/framer/images/` — imagens `.webp`/`.png`, incluindo todas as
  variantes de `srcset` (512/1024/2048/4096)

Total: 91 arquivos, ~8,3 MB.

## O que continua apontando para fora

Preservado como no original — troque os IDs/URLs se a cópia for ao ar:

- **GTM / tracker**: `tracker.corporalacademy.com.br`, `load.tracker.corporalacademy.com.br`
- **Microsoft Clarity**: `clarity.ms`
- **Framer Analytics**: `events.framer.com` (site-id do original)
- **Formulário**: envia os leads para o Leadhero (`ilzpqehbbxvmogorpfma.supabase.co/functions/v1/lead-capture-api/5cc44ae2-…`)
- **Grupo de WhatsApp**: `sndflw.com/i/blackfridaycorporalclass` (SendFlow, rotador de convites)
- **Outros CTAs**: link do Google Drive
- **intl-tel-input** (máscara de telefone): `cdn.jsdelivr.net`

## Observação

Ao rolar, o runtime do Framer monta uma URL de imagem otimizada e gera um
404 em `/images/…webp`. Isso também não afeta o render (a imagem já foi
carregada pelo `srcset`), e o mesmo tipo de erro de console aparece no site
original.

## Publicar (Cloudflare Pages)

| Campo | Valor |
|---|---|
| Framework preset | `None` |
| Build command | *(vazio)* |
| Build output directory | **`site`** |

Projeto: `corporal-lives-black-november-setembro-26` · no ar em
<https://corporal-lives-black-november-setembro-26.pages.dev>

O output **precisa** ser `site`, não a raiz — senão o README do repositório, o
`.gitignore` e o `docs/` também vão para o ar.

`docs/` fica fora da pasta publicada justamente por isso: antes a documentação
morava em `site/README.md` e teria ficado acessível em `seudominio/README.md`,
junto com o `index.html.bak`, que ainda carrega o snippet antigo do WhatsApp e
a URL da Clint.

**Pegadinha:** salvar a configuração de build **não** republica o site. Depois
de mudar o output directory é preciso ir em *Deployments* e usar *Retry
deployment* — ou fazer um push na `main`, que dispara build automático. Com o
output apontando para a raiz, a home dá 404 (não existe `index.html` lá) e o
`docs/` fica público; se `/docs/README.md` responder 200, o deploy no ar ainda
é o antigo.

Publicado, `site/obrigado/index.html` responde em `/obrigado` e `/obrigado/`.
São 95 arquivos e 9 MB, com o maior em 3,4 MB — folgado nos limites do Pages
(25 MiB por arquivo).

## Botão do formulário (página de captura)

Texto `Continuar` → **`CADASTRAR AGORA`**, cor roxa → verde WhatsApp, mais o
mesmo pulso da página de obrigado. Mexe em três lugares:

1. **`index.html`** — o markup SSR (gradiente inline + texto), para não piscar a
   versão roxa antes da hidratação; e um `<style data-pulso-cadastro>` no
   `<head>` com o pulso (`.fa-submit`), incluindo `prefers-reduced-motion`.
2. **`ZQn_pCuEGX….mjs`** — props da instância: `textos.buttonSubmit` e as cores
   `buttonFrom` / `buttonTo` / `buttonHoverFrom` / `buttonHoverTo`.
3. **`CorporalAcademyForm.BB9HNbDW.mjs`** — o brilho do hover.

### Por que o componente precisou ser tocado

O CSS de hover do botão é injetado em runtime pelo componente (`.fa-form-<id>
.fa-submit:hover`, com `!important`), e o brilho vinha de `${h.focusRing}` —
a mesma cor do anel de foco dos inputs. Pintar `focusRing` de verde deixaria os
inputs verdes junto, então o brilho do botão virou um valor fixo
`rgba(37, 211, 102, 0.45)` no template; os dois `${h.focusRing}` dos inputs
seguem intactos (rosa).

Tentei antes passar uma cor nova por prop (`cores.buttonShadow`), mas o Framer
descarta chaves não declaradas: `cores` é um `p.Object` com 15 sub-controles
fixos e `buttonShadow` não é um deles. Se for refazer isso no Framer, ou
declara o sub-controle novo no código do componente, ou aceita que o valor
fique fixo — lembrando que o componente é compartilhado com outras páginas.

### Ao testar local

O `python3 -m http.server` responde `304` nos `.mjs`, então o navegador
continua servindo a versão antiga depois de editar um módulo. Recarregar a
página não basta — force com `fetch(url, {cache:'reload'})` no console ou use
hard reload.

## Google Tag Manager

O snippet (server-side, via Stape) é **o mesmo nas duas páginas** — container
`7azhvavtgco`, em `load.tracker.corporalacademy.com.br` com fallback em
`tracker.corporalacademy.com.br`.

- **Página de captura**: já vinha com ele, no `headStart` (snippet `hM1S_HycZ`).
  Byte a byte igual ao da página de obrigado. **Não instale de novo aqui** —
  duplicaria todos os disparos.
- **Página de obrigado**: instalado no topo do `<head>`, logo depois do
  `<meta name="robots">` e antes do `<style>` das fontes.

Testado: `dataLayer` recebe `gtm.js`, `gtm.dom` e `gtm.load`, e o container
dispara GA4 (`G-CQ1982VGZT`) e o Pixel da Meta (`892371957108000`).

A tag `<noscript>` com o iframe não faz parte desse snippet — é o padrão em
GTM server-side, não é esquecimento.

## Botão flutuante de WhatsApp — removido

O snippet `DMegGI9Pe` ("WhatsApp Float Button") do `bodyEnd` foi removido a
pedido; no lugar ficou um comentário HTML marcando onde estava. Ele não era
componente do Framer, e sim um `<script>` custom que:

- injetava um `<a class="wa-btn">` fixo (60×60, verde, `z-index: 900000`) no
  canto inferior direito, com pulso próprio (`wa-pulse`);
- só aparecia depois de 300px de rolagem;
- alternava o destino pelo horário de Brasília — `comercial-corporal-class`
  de seg a sex das 8h às 17h59, `marina-corporal-class` nas noites e fins de
  semana — ambos em `lh.facialacademy.com.br`, e recalculados no clique.

Com isso saíram do projeto as 6 ocorrências de `wa-btn`, as 2 de
`botao_whatsapp` e as duas URLs da Facial Academy.

Para trazer de volta, copie **só o bloco** do `docs/index.html.bak` (entre
`<!-- Snippet: DMegGI9Pe -->` e `<!-- SnippetEnd: DMegGI9Pe -->`) ou pegue do
site original. **Não restaure o `.bak` inteiro**: ele é uma foto anterior às
mudanças do botão do formulário — nele o botão ainda é roxo e diz
"Continuar".

Detalhe que valia registrar: o gatilho de 300px era um **fallback**. O script
tentava calcular a altura de um elemento `.gpc-b` que não existe nesta página
— resquício de outro template.

## Integração do formulário: só Leadhero

O formulário disparava para **dois** destinos a cada envio. Os dois foram
removidos e no lugar ficou apenas o Leadhero:

| Destino | Onde estava | Situação |
|---|---|---|
| Clint (webhook) | `webhooks[]` nas props da página | removido |
| ActiveCampaign | chamada fixa no componente | removido |
| **Leadhero** | `webhooks[]` nas props da página | **ativo** |

Endpoint: `https://ilzpqehbbxvmogorpfma.supabase.co/functions/v1/lead-capture-api/5cc44ae2-2fdd-40bc-ad71-6d3e5ab2466e`

O ActiveCampaign (`corporalacademy.activehosted.com`, formulário 33) não tinha
toggle — era um `fetch` incondicional dentro do
`CorporalAcademyForm.BB9HNbDW.mjs`. Precisou sair no próprio componente, que
no Framer é **compartilhado com outras páginas**: lá o certo é desativar na
instância desta página, não apagar do componente.

### Payload que o Leadhero recebe

`POST` com JSON:

    {
      "timestamp": "2026-09-15T00:10:00.000Z",
      "fullname": "...", "email": "...", "phone": "+5541...",
      "utm_source": "...", "utm_campaign": "...", "utm_medium": "...",
      "utm_content": "...", "utm_term": "...", "utmid": "...",
      "sck": "", "xcod": "", "src": "...",
      "fbclid": "...", "gclid": "...", "wbraid": "", "gbraid": "",
      "_webhookName": "Leadhero"
    }

As UTMs continuam sendo lidas da URL e enviadas aqui — o que foi desligado
antes (`appendDataToRedirect`) só afeta a barra de endereço, não o payload.

### Teste de envio — feito, funcionando

Testado em 14/09/2026, quatro envios:

| # | Como | Content-Type | Resultado |
|---|---|---|---|
| A | curl direto | `application/json` | HTTP 200, lead salvo |
| B | curl direto | `text/plain` | HTTP 200, lead salvo |
| C | formulário real | (navegador) | disparou + redirecionou |
| D | formulário real | (navegador) | disparado e capturado |

Resposta do endpoint nos testes A e B:

    {"success":true,"lead_id":"…","tags_applied":["ca | black vitalícia | leads"],
     "pipeline_id":"c89d9cbb-a98d-4fcf-bdeb-109be4c8c10e","message":"Lead … salvo com sucesso."}

**O `no-cors` não é problema:** o teste B provou que o endpoint aceita o corpo
com `text/plain`, que é como o navegador manda. Não precisa trocar para
`mode: 'cors'`. O endpoint também **não exige header de autorização**.

No teste D o `fetch` foi interceptado para capturar o disparo real do
formulário. Confirmado: **uma** requisição, para o endpoint do Leadhero, com
`fullname`, `email`, `phone` (+55 completo) e `_webhookName: "Leadhero"`.
**Nenhuma** requisição para `activehosted` — o ActiveCampaign está mesmo fora.

Os quatro leads de teste ficaram na base (`TESTE Claude Code A` a `D`,
e-mails `teste.integracao.*@exemplo-invalido.test`) — apague quando quiser.

O redirect também foi confirmado: leva para
`corporalacademy.com.br/obrigado/`, que hoje responde 404 porque a página de
obrigado ainda não foi publicada lá.

## Mobile: formulário na primeira dobra

`<style data-mobile-primeira-dobra>` no `<head>` do `index.html`, em duas
faixas. Só comprime respiro — nenhum conteúdo foi removido ou reordenado, e o
desktop não é tocado (conferido: 141 elementos e 3372px de altura, iguais ao
original).

**Faixa 1 — `max-width: 809.98px`** (o mesmo breakpoint da variante mobile do
Framer): topo da seção 80→16, gaps do bloco de texto 50→18, card 32→18, e
"Preencha com seus dados" de 28px→22px, que o faz caber em uma linha.

**Faixa 2 — `+ max-height: 740px`**, para celular real: lockup a 72% da
largura (escala por `aspect-ratio`), H1 38→29px, parágrafo 16→14,5px, espaço
rótulo→campo 8→4 e entre campos 18→10.

| Viewport | Antes | Depois | Folga |
|---|---|---|---|
| 375×812 | 967 | 756 | 56px |
| 375×650 (iPhone com barras) | 967 | 626 | 24px |
| 360×640 (Android pequeno) | — | 623 | 17px |

(posição da base do botão CADASTRAR AGORA)

Os campos mantêm 48px de altura e o botão 44px — as áreas de toque não foram
reduzidas. Tentei cortar altura do input pelo `padding`, mas não adianta: ele
tem `height:46px` inline, então o espaço veio do rótulo.

Se um dia precisar de mais espaço na dobra, o próximo corte natural é mover o
parágrafo de abertura ("A Black Vitalícia… está chegando") para baixo do
formulário no mobile — sozinho ele vale ~85px.

## Página de obrigado (`/obrigado`)

`obrigado/index.html` — uma dobra, objetivo único: entrar no grupo de WhatsApp.
Fonte Silka e imagens em base64; o único recurso externo é o GTM (veja
abaixo). `noindex` no `<head>`.

Testada sem rolagem em desktop (964px), iPhone (375×812) e tela baixa
(360×640) — a regra `@media (max-height:740px)` compacta tudo para caber.

Botão → `https://sndflw.com/i/blackfridaycorporalclass` (SendFlow), em verde
WhatsApp (`#25D366` → `#128C7E`) com pulso contínuo.

O pulso é um anel de `box-shadow` — não usa `transform`, então não briga com o
`:active` do botão e não ocupa espaço no layout (nenhum dos três tamanhos
ganhou rolagem). Para no `:hover` (quem já achou o botão não precisa mais ser
chamado) e é desligado inteiro em `prefers-reduced-motion`.

Contraste: branco sobre esse verde dá ~2,8:1, abaixo dos 4,5:1 da WCAG AA. É
o par de cores oficial do WhatsApp e o que a pessoa reconhece na hora; se
preferir passar no AA, dá para escurecer o gradiente (algo como `#1EA85A` →
`#0E7263`) mantendo a leitura de "verde WhatsApp".

### Segunda versão: `/aguarde-black-vitalicia`

`site/aguarde-black-vitalicia/index.html` — mesma identidade, sem CTA e **sem
nenhum link**. Todo o conteúdo é:

> **Obrigado.**
> Cadastro recebido com sucesso
> Em breve divulgaremos todos os detalhes!

Para quem não deve ser mandado ao grupo. Reaproveita as fontes Silka e as
imagens em base64 da outra página, então também é autocontida — só o GTM é
externo, com o mesmo container.

Sem rolagem em desktop, 375×812 e 360×640.

Cada frase do texto de apoio fica em um `<span>` com `display:block`. Não é
enfeite: com as duas frases separadas por `<br>`, o `text-wrap: balance` não
atua (o navegador trata o bloco inteiro) e no celular sobrava "detalhes!"
sozinho na última linha. Em blocos separados, o balanceamento funciona em cada
frase.

O `<title>` continua "Obrigado — …", por decisão — não acompanha o nome do
caminho.

### Redirect do formulário

O formulário da captura passou a apontar para esta página. Alterado em:

    assets/framer/sites/45N5zIUFvvG4RXjjHh9BaL/ZQn_pCuEGXCHcar7NVfMBk2G1IrvVKi_hBNVDcJYMck.DvmSjc0R.mjs

    redirectUrl:`https://sndflw.com/i/blackfridaycorporalclass`   (antes)
    redirectUrl:`/obrigado/`                                      (agora)

**Atenção:** rodar o script de espelhamento de novo sobrescreve essa
alteração (e todas as outras feitas nos `.mjs`).

O componente original **não aceitava caminho relativo**: ele prefixa `https://`
em valores que não começam com `http`, então `/obrigado/` virava
`https://obrigado/` — host inválido, funil quebrado. Por isso o
`CorporalAcademyForm.BB9HNbDW.mjs` ganhou uma linha: caminho iniciado por `/`
passa a resolver contra `location.origin`.

Com isso o redirect funciona em qualquer domínio — `pages.dev`, domínio final
e localhost — sem precisar trocar nada ao publicar.

No site real a mudança tem que ser feita no Framer, na propriedade
**Comportamento → redirectUrl** do componente do formulário.

### Dados pessoais na URL — resolvido

O formulário vinha com `appendDataToRedirect: true`, herdado de quando o
redirect ia para o checkout (onde servia para pré-preencher). Com o destino
agora sendo a página de obrigado, isso só jogava nome, e-mail e telefone do
lead na URL:

    /obrigado/?name=Maria+Silva&email=maria%40exemplo.com&phoneNumber=5541999999999&checkoutMode=10

Desligado (`appendDataToRedirect: !1` no módulo da página). O redirect agora é
só `https://corporalacademy.com.br/obrigado/`, limpo.

Efeito colateral: as UTMs também deixam de ser repassadas na URL — elas eram
anexadas dentro do mesmo bloco de código. Como a página de obrigado agora tem
GTM, vale saber que a atribuição **não depende** desses parâmetros: as duas
páginas ficam no mesmo domínio, então a sessão do GA4 e os cookies `_fbp` /
`_fbc` da Meta atravessam o redirect sozinhos. O que se perde é só a UTM
visível na URL da página de obrigado.

Os leads seguem indo completos para a integração — o que mudou foi só o que
viaja na barra de endereço.

