# vsvideo-skill â€” Virtual Staging Video

## ðŸ“– Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/vsvideo-skill/guia/**

---

Skill que transforma **uma foto/render de interior finalizado** num **vÃ­deo time-lapse de reforma** (antes â†’ depois), sem ediÃ§Ã£o manual.

## Como funciona

1. Analisa a imagem enviada (cÃ¢mera, perspectiva, arquitetura, materiais, luz, estilo).
2. Gera a versÃ£o **antes da construÃ§Ã£o** do mesmo ambiente â€” mesma cÃ¢mera, mesma arquitetura, sÃ³ o acabamento muda.
3. Valida a consistÃªncia (Ã¢ngulo/layout). Se divergir, regenera â€” antes de gastar a etapa cara.
4. Gera o **vÃ­deo de reforma** interpolando "antes" â†’ "depois" como par de keyframes.

O motor de cada etapa depende da skill escolhida (tabela abaixo).

## TrÃªs skills, um motor cada

| # | Skill | Imagem "antes" | VÃ­deo | Custo |
|---|---|---|---|---|
| 1 | `higgsfield` (original) | GPT Image (ferramenta do agente) | Higgsfield Seedance 2.0 Mini (MCP) | conforme a conta |
| 2 | `agnes` | `agnes-image-2.1-flash` (img2img) | `agnes-video-v2.0` (keyframes) | **US$ 0** |
| 3 | `kling` | `kling-image-v3_0` (ou `gpt-image2`) | `kling-video-v2_5` com `--tailImage` | **crÃ©ditos Kling** |

Medido em 2026-07-28, na mesma sala: **melhor vÃ­deo = `kling`** (operÃ¡rios crÃ­veis, aterrissa no
keyframe B, mas com marca d'Ã¡gua KlingAI); **melhor custo = `agnes`** (US$ 0, operÃ¡rios borrados);
**melhor "antes" = `kling-image-v3_0`**.

Prompts, portÃ£o de consistÃªncia e caminhos de saÃ­da sÃ£o idÃªnticos nas trÃªs â€” o que muda Ã© o
motor. Escolha pelo nome ao pedir ("faz o vÃ­deo de reforma pelo Kling").

```bash
python3 skills/agnes/rodar.py                          # Agnes completo (US$ 0)
python3 skills/agnes/rodar.py --img gpt-image2 --sim   # GPT Image 2 na imagem + vÃ­deo Agnes
python3 skills/kling/rodar.py --sim                    # Kling nos dois (crÃ©dito)
python3 skills/higgsfield/rodar.py                     # prepara os handoffs do fluxo original
```

## Estrutura â€” trÃªs sistemas independentes

```text
vsvideo-skill/
â”œâ”€â”€ input/interior-design.png     # a imagem do interior finalizado
â”œâ”€â”€ output/                       # tudo que Ã© gerado
â”œâ”€â”€ skills/
â”‚   â”œâ”€â”€ higgsfield/               # 1 â€” SKILL.md + rodar.py + prompts.py
â”‚   â”œâ”€â”€ agnes/                    # 2 â€” SKILL.md + rodar.py + client.py + prompts.py
â”‚   â””â”€â”€ kling/                    # 3 â€” SKILL.md + rodar.py + prompts.py
â””â”€â”€ doc/                          # tutorial (PT/EN) + spec original + PLANO-AGNES.md
```

Cada pasta tem o prÃ³prio cÃ³digo e os prÃ³prios prompts â€” **nenhuma importa a outra**. Mexer
numa nÃ£o quebra as demais (o preÃ§o Ã© a duplicaÃ§Ã£o intencional dos prompts).

## InstalaÃ§Ã£o

Copie a pasta da skill para o diretÃ³rio de skills do seu agente:

```bash
cp -r skills/{higgsfield,agnes,kling} ~/.claude/skills/
```

Ou trabalhe direto dentro deste repo â€” o agente encontra a skill em `skills/`.

## Uso

TrÃªs formas de dar o comando.

### 1. Linguagem natural (o normal)

```bash
cd ~/projetos/vsvideo-skill
cp ~/Downloads/sala.png input/interior-design.png
claude
```

E, no chat:

> faz o vÃ­deo de reforma dessa imagem

Gatilhos que acionam a skill: *vÃ­deo de reforma*, *virtual staging*, *antes e depois do ambiente*, *time-lapse de obra*, *transformar foto de interior em vÃ­deo de reforma*.

### 2. Anexando a imagem no chat

Sem usar `input/` â€” arraste a imagem e peÃ§a:

> [imagem anexada] transforma isso num vÃ­deo de reforma

### 3. ForÃ§ando a skill pelo nome

Quando nÃ£o quiser depender do gatilho:

> usa a skill agnes na imagem em input/interior-design.png

Trocando o nome, vocÃª escolhe o motor: `agnes`, `higgsfield`
ou `kling`. Instaladas em `~/.claude/skills/`, as trÃªs tambÃ©m respondem como
slash command:

```
/agnes
/higgsfield
/kling
```

Antes de rodar: em `/higgsfield`, confira o MCP com `/mcp` â€” sem o Higgsfield conectado a skill
para nas duas imagens. Em `/kling`, lembre que cada submissÃ£o gasta crÃ©dito.

SaÃ­das em `output/`:

```text
output/before-construction.png   # casca de obra
output/completed-interior.png    # o ambiente pronto
output/renovation-video.mp4      # o time-lapse
```

## PrÃ©-requisitos

- `AGNES_API_KEY` em `~/projetos/agnes-nei/.env` (API Agnes AI, custo US$ 0).
- `ffmpeg` / `ffprobe` no PATH.
- Python 3 (sÃ³ a biblioteca padrÃ£o â€” sem dependÃªncias).

Modelos usados: `agnes-image-2.1-flash` (img2img, imagem "antes") e `agnes-video-v2.0`
(`mode:"keyframes"`, vÃ­deo antesâ†’depois).

> âš ï¸ No plano free da Agnes, seus dados podem ser usados para treinar os modelos.
> NÃ£o envie imagem de cliente que seja confidencial.

### Origem da imagem dentro do Agnes

O vÃ­deo Ã© sempre Agnes; a imagem "antes" tem quatro origens:

| `--img` | Como funciona | Custo |
|---|---|---|
| `agnes` (default) | img2img da prÃ³pria Agnes | **US$ 0** |
| `gpt-image2` | GPT Image 2 pelo CLI do Kling â€” gera lÃ¡, **baixa aqui** | crÃ©dito Kling |
| `kling` | `kling-image-v3_0` pelo CLI do Kling â€” idem | crÃ©dito Kling |
| `agente` | GPT Image do agente, por handoff | â€” |

As duas do meio sÃ³ submetem com `--sim`.

```bash
LAYOUT="one large window on the whole left wall, blank back wall with no openings" \
  python3 skills/agnes/rodar.py --so-imagem
```

## DocumentaÃ§Ã£o

- `doc/SKILL.md` â€” especificaÃ§Ã£o original (prompts completos).
- `doc/virtual_staging_renovation_video_pt.md` â€” tutorial em portuguÃªs.
- `doc/virtual_staging_renovation_video_en.md` â€” tutorial em inglÃªs.

---

INEMA Â· [inema.club](https://inema.club)

---

Projeto adaptado da comunidade INEMA TDS - Marca Arckhe