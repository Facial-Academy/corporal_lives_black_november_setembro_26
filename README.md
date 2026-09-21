# corporal_lives_black_november_setembro_26

Cópia da landing de captação da **Black Vitalícia** (Corporal Academy) e a
página de obrigado do funil.

A captação é uma cópia fiel da página Framer publicada em
`corporalacademy.com.br/black-friday-2026` — HTML renderizado no servidor,
mais os módulos, fontes e imagens baixados, de modo que roda offline e com os
efeitos originais (smooth scroll, animações de entrada, reveal no scroll).

## Rodar

```bash
python3 -m http.server 4188 --directory site
```

- Captação: <http://localhost:4188>
- Obrigado: <http://localhost:4188/obrigado/>

Precisa ser servido por HTTP. Abrir o `index.html` por `file://` quebra os
módulos ES.

## Estrutura

| Caminho | O que é |
|---|---|
| `site/index.html` | página de captação (cópia Framer) |
| `site/obrigado/index.html` | página de obrigado, uma dobra |
| `site/aguarde-black-vitalicia/index.html` | 2ª versão: só a confirmação |
| `site/assets/framer/` | 16 módulos `.mjs`, 50 fontes e as imagens |
| `docs/README.md` | **a documentação de verdade** |

## Publicar

No ar em <https://corporal-lives-black-november-setembro-26.pages.dev>.

Cloudflare Pages, sem build: preset `None`, build command vazio e **build
output directory `site`**. Não aponte para a raiz — o `docs/` e o README do
repositório iriam junto para o ar.

Deploy automático a cada push na `main`.

O redirect do formulário é relativo (`/obrigado/`), então funciona em qualquer
domínio: `pages.dev`, domínio final ou localhost.

## O funil

```
formulário  →  Leadhero  →  /obrigado  →  grupo de WhatsApp (SendFlow)
```

## Documentação

Tudo o que foi alterado sobre a cópia original está em
**[docs/README.md](docs/README.md)**: o que mudou, por quê, e as armadilhas
que apareceram no caminho — entre elas o cache dos `.mjs` que faz o navegador
servir a versão antiga, o componente que recusava caminho relativo no redirect,
e a cor de foco compartilhada entre o botão e os inputs.

Vale ler antes de mexer em qualquer coisa dentro de `site/assets/`.

> **Atenção:** as alterações nos `.mjs` valem para esta cópia. No site real
> elas precisam ser refeitas no Framer — e o componente do formulário é
> compartilhado com outras páginas.
