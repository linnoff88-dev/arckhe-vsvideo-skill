ï»¿# vsvideo-skill ê¢â¬â Virtual Staging Video

## ê°Å¸ââ Guia de uso

Guia completo (landing + passo a passo): **https://linnoff88-dev.github.io/arckhe-/guia/**

---

Skill que transforma **uma foto/render de interior finalizado** num **vêÂ­deo time-lapse de reforma** (antes ê¢â â depois), sem ediêÂ§êÂ£o manual.

## Como funciona

1. Analisa a imagem enviada (cêÂ¢mera, perspectiva, arquitetura, materiais, luz, estilo).
2. Gera a versêÂ£o **antes da construêÂ§êÂ£o** do mesmo ambiente ê¢â¬â mesma cêÂ¢mera, mesma arquitetura, sêÂ³ o acabamento muda.
3. Valida a consistêÂªncia (êÂ¢ngulo/layout). Se divergir, regenera ê¢â¬â antes de gastar a etapa cara.
4. Gera o **vêÂ­deo de reforma** interpolando "antes" ê¢â â "depois" como par de keyframes.

O motor de cada etapa depende da skill escolhida (tabela abaixo).

## TrêÂªs skills, um motor cada

| # | Skill | Imagem "antes" | VêÂ­deo | Custo |
|---|---|---|---|---|
| 1 | `higgsfield` (original) | GPT Image (ferramenta do agente) | Higgsfield Seedance 2.0 Mini (MCP) | conforme a conta |
| 2 | `agnes` | `agnes-image-2.1-flash` (img2img) | `agnes-video-v2.0` (keyframes) | **US$ 0** |
| 3 | `kling` | `kling-image-v3_0` (ou `gpt-image2`) | `kling-video-v2_5` com `--tailImage` | **crêÂ©ditos Kling** |

Medido em 2026-07-28, na mesma sala: **melhor vêÂ­deo = `kling`** (operêÂ¡rios crêÂ­veis, aterrissa no
keyframe B, mas com marca d'êÂ¡gua KlingAI); **melhor custo = `agnes`** (US$ 0, operêÂ¡rios borrados);
**melhor "antes" = `kling-image-v3_0`**.

Prompts, portêÂ£o de consistêÂªncia e caminhos de saêÂ­da sêÂ£o idêÂªnticos nas trêÂªs ê¢â¬â o que muda êÂ© o
motor. Escolha pelo nome ao pedir ("faz o vêÂ­deo de reforma pelo Kling").

```bash
python3 skills/agnes/rodar.py                          # Agnes completo (US$ 0)
python3 skills/agnes/rodar.py --img gpt-image2 --sim   # GPT Image 2 na imagem + vêÂ­deo Agnes
python3 skills/kling/rodar.py --sim                    # Kling nos dois (crêÂ©dito)
python3 skills/higgsfield/rodar.py                     # prepara os handoffs do fluxo original
```

## Estrutura ê¢â¬â trêÂªs sistemas independentes

```text
vsvideo-skill/
ê¢âÅê¢ââ¬ê¢ââ¬ input/interior-design.png     # a imagem do interior finalizado
ê¢âÅê¢ââ¬ê¢ââ¬ output/                       # tudo que êÂ© gerado
ê¢âÅê¢ââ¬ê¢ââ¬ skills/
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ higgsfield/               # 1 ê¢â¬â SKILL.md + rodar.py + prompts.py
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ agnes/                    # 2 ê¢â¬â SKILL.md + rodar.py + client.py + prompts.py
ê¢ââ   ê¢ââê¢ââ¬ê¢ââ¬ kling/                    # 3 ê¢â¬â SKILL.md + rodar.py + prompts.py
ê¢ââê¢ââ¬ê¢ââ¬ doc/                          # tutorial (PT/EN) + spec original + PLANO-AGNES.md
```

Cada pasta tem o prêÂ³prio cêÂ³digo e os prêÂ³prios prompts ê¢â¬â **nenhuma importa a outra**. Mexer
numa nêÂ£o quebra as demais (o preêÂ§o êÂ© a duplicaêÂ§êÂ£o intencional dos prompts).

## InstalaêÂ§êÂ£o

Copie a pasta da skill para o diretêÂ³rio de skills do seu agente:

```bash
cp -r skills/{higgsfield,agnes,kling} ~/.claude/skills/
```

Ou trabalhe direto dentro deste repo ê¢â¬â o agente encontra a skill em `skills/`.

## Uso

TrêÂªs formas de dar o comando.

### 1. Linguagem natural (o normal)

```bash
cd ~/projetos/vsvideo-skill
cp ~/Downloads/sala.png input/interior-design.png
claude
```

E, no chat:

> faz o vêÂ­deo de reforma dessa imagem

Gatilhos que acionam a skill: *vêÂ­deo de reforma*, *virtual staging*, *antes e depois do ambiente*, *time-lapse de obra*, *transformar foto de interior em vêÂ­deo de reforma*.

### 2. Anexando a imagem no chat

Sem usar `input/` ê¢â¬â arraste a imagem e peêÂ§a:

> [imagem anexada] transforma isso num vêÂ­deo de reforma

### 3. ForêÂ§ando a skill pelo nome

Quando nêÂ£o quiser depender do gatilho:

> usa a skill agnes na imagem em input/interior-design.png

Trocando o nome, vocêÂª escolhe o motor: `agnes`, `higgsfield`
ou `kling`. Instaladas em `~/.claude/skills/`, as trêÂªs tambêÂ©m respondem como
slash command:

```
/agnes
/higgsfield
/kling
```

Antes de rodar: em `/higgsfield`, confira o MCP com `/mcp` ê¢â¬â sem o Higgsfield conectado a skill
para nas duas imagens. Em `/kling`, lembre que cada submissêÂ£o gasta crêÂ©dito.

SaêÂ­das em `output/`:

```text
output/before-construction.png   # casca de obra
output/completed-interior.png    # o ambiente pronto
output/renovation-video.mp4      # o time-lapse
```

## PrêÂ©-requisitos

- `AGNES_API_KEY` em `~/projetos/agnes-nei/.env` (API Agnes AI, custo US$ 0).
- `ffmpeg` / `ffprobe` no PATH.
- Python 3 (sêÂ³ a biblioteca padrêÂ£o ê¢â¬â sem dependêÂªncias).

Modelos usados: `agnes-image-2.1-flash` (img2img, imagem "antes") e `agnes-video-v2.0`
(`mode:"keyframes"`, vêÂ­deo antesê¢â âdepois).

> ê¢Å¡Â ê¯Â¸Â No plano free da Agnes, seus dados podem ser usados para treinar os modelos.
> NêÂ£o envie imagem de cliente que seja confidencial.

### Origem da imagem dentro do Agnes

O vêÂ­deo êÂ© sempre Agnes; a imagem "antes" tem quatro origens:

| `--img` | Como funciona | Custo |
|---|---|---|
| `agnes` (default) | img2img da prêÂ³pria Agnes | **US$ 0** |
| `gpt-image2` | GPT Image 2 pelo CLI do Kling ê¢â¬â gera lêÂ¡, **baixa aqui** | crêÂ©dito Kling |
| `kling` | `kling-image-v3_0` pelo CLI do Kling ê¢â¬â idem | crêÂ©dito Kling |
| `agente` | GPT Image do agente, por handoff | ê¢â¬â |

As duas do meio sêÂ³ submetem com `--sim`.

```bash
LAYOUT="one large window on the whole left wall, blank back wall with no openings" \
  python3 skills/agnes/rodar.py --so-imagem
```

## DocumentaêÂ§êÂ£o

- `doc/SKILL.md` ê¢â¬â especificaêÂ§êÂ£o original (prompts completos).
- `doc/virtual_staging_renovation_video_pt.md` ê¢â¬â tutorial em portuguêÂªs.
- `doc/virtual_staging_renovation_video_en.md` ê¢â¬â tutorial em inglêÂªs.

---

ARCKHÉ êÂ· [ARCKHÉ AI](https://ARCKHÉ AI)

---

Projeto  - ARCKHÉe