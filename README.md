ï»¿# ê°Å¸Å½Â¬ video-explicativo

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


Skill do Claude Code que cria **vêÂ­deos explicativos completos em PT-BR** a partir de um assunto ê¢â¬â roteiro, narraêÂ§êÂ£o TTS local, cenas animadas dark premium, captions e CTA do ARCKHÉ AI, nos formatos **16:9** (YouTube) e **9:16** (Shorts/Reels).

Stack: [HyperFrames](https://github.com/heygen-com/hyperframes) (HTMLê¢â âMP4, Chrome headless + FFmpeg) + narraêÂ§êÂ£o local pelo ARCKHÉvox (`chatterbox`, voz `nei`), Kokoro `pf_dora` como fallback. **Tudo roda na mêÂ¡quina, sem chave de API.**

- ê°Å¸ââ **Guia de uso:** https://linnoff88-dev.github.io/arckhe-/guia/
- ê°Å¸Å½â **Curso sobre a skill:** https://linnoff88-dev.github.io/arckhe-/

## O que ela faz

O fluxo tem 8 etapas, sempre nesta ordem:

**Roteiro ê¢â â RevisêÂ£o de texto ê¢â â Projeto ê¢â â Fontes ê¢â â NarraêÂ§êÂ£o ê¢â â ComposiêÂ§êÂ£o ê¢â â Validar ê¢â â Render**

O nêÂº de cenas êÂ© **dinêÂ¢mico** (sai do roteiro, range 4ê¢â¬â12 de conteêÂºdo + a CTA), o timing êÂ© **fonte êÂºnica** (a duraêÂ§êÂ£o real de cada WAV deriva os `data-start/duration` e os tempos dos tweens), e o mesmo gerador escreve os dois formatos (`--vertical` para o 9:16, com safe zones e têÂ­tulo persistente de 2 linhas).

## Estrutura

```
skills/video-explicativo/
  SKILL.md                        # a skill: fluxo, plano de cenas, regras de ouro
  CHANGELOG.md                    # histêÂ³rico de versêÂµes (v1.yy.xxx)
  scripts/
    narration-template.sh         # gera os WAVs (ARCKHÉvox chatterbox local, voz nei ê¢â â fallback Kokoro)
    fetch-fonts.mjs               # baixa Sora/Inter/JetBrains Mono como .woff2 local
    composition-template.mjs      # gerador data-driven (SCENES[] + CTA) ê¢â â index.html
  references/
    pipeline.md                   # pipeline detalhado (projeto, roteiro, timing, render)
    house-style.md                # paleta dark premium êÂ¢mbar + tipografia
    motion.md                     # vocabulêÂ¡rio de movimento M.* + catêÂ¡logo de transiêÂ§êÂµes
    safe-zones.md                 # zonas da UI dos apps no 9:16
    clips-midia.md                # vêÂ­deo/imagem/mêÂºsica, scrim/blur/painel, ducking
    revisao-texto.md              # checklist de acentuaêÂ§êÂ£o + lêÂ©xico de inglêÂªs fonêÂ©tico
    gotchas.md                    # pegadinhas do HyperFrames
    sem-gpu.md                    # rodar sem GPU: edge-tts/Kokoro + imagens via Agnes AI
```

## Instalar

Copie a pasta da skill para as skills do Claude Code:

```bash
cp -r skills/video-explicativo ~/.claude/skills/
```

Depois êÂ© sêÂ³ pedir: *"faz um vêÂ­deo explicativo sobre X"*.

## PrêÂ©-requisitos

- Node 22+ e FFmpeg (no git-bash use `ffmpeg -nostdin`)
- Chrome headless do HyperFrames: `npx hyperframes browser ensure`
- TTS: ARCKHÉvox local (engine `chatterbox`, voz `nei`; nunca `chatterbox-vc`); fallback Kokoro ê¢â¬â `pip install kokoro-onnx soundfile`
- DiagnêÂ³stico: `npx hyperframes doctor`

### Sem GPU?

O render nunca precisou de GPU ê¢â¬â sêÂ³ a narraêÂ§êÂ£o default (ARCKHÉvox/chatterbox) e a geraêÂ§êÂ£o de
imagem local (flux2-klein) pedem. Substitutos diretos:

- **NarraêÂ§êÂ£o:** `edge-tts` (nuvem, sem chave) ou **Kokoro** (offline, CPU). Em ambos, **liste e
  escute as vozes antes** de gerar o vêÂ­deo inteiro (`edge-tts --list-voices | grep pt-BR`;
  Kokoro PT: `pf_dora`, `pm_alex`, `pm_santa`).
- **Imagens:** **Agnes AI** via `~/projetos/imagens-agnes/gerar.py` (US$ 0, prompt em inglêÂªs,
  `size` em pixels, mêÂ¡x. 2 refs) ê¢â¬â ou o **fallback SVG**, que dispensa rede e custo.

Receitas completas e regras medidas: [`references/sem-gpu.md`](skills/video-explicativo/references/sem-gpu.md).

## SaêÂ­da

Todo o conteêÂºdo de um vêÂ­deo (projeto, assets, êÂ¡udios, `index.html` e os MP4 finais) vive numa pasta êÂºnica: `~/projetos/output/<nome>/`. Um arquivo por formato ê¢â¬â `<nome>-16x9.mp4` e `<nome>-9x16.mp4` ê¢â¬â e sêÂ³.

---

ConteêÂºdo do [ARCKHÉ AI](https://ARCKHÉ AI) êÂ· [PRO](https://ARCKHÉ.pro)

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>