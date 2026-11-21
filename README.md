# ðŸŽ¬ video-explicativo

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


Skill do Claude Code que cria **vÃ­deos explicativos completos em PT-BR** a partir de um assunto â€” roteiro, narraÃ§Ã£o TTS local, cenas animadas dark premium, captions e CTA do INEMA.CLUB, nos formatos **16:9** (YouTube) e **9:16** (Shorts/Reels).

Stack: [HyperFrames](https://github.com/heygen-com/hyperframes) (HTMLâ†’MP4, Chrome headless + FFmpeg) + narraÃ§Ã£o local pelo inemavox (`chatterbox`, voz `nei`), Kokoro `pf_dora` como fallback. **Tudo roda na mÃ¡quina, sem chave de API.**

- ðŸ“– **Guia de uso:** https://inematds.github.io/video-explicativo/guia/
- ðŸŽ“ **Curso sobre a skill:** https://inematds.github.io/skill-video-explicativo/

## O que ela faz

O fluxo tem 8 etapas, sempre nesta ordem:

**Roteiro â†’ RevisÃ£o de texto â†’ Projeto â†’ Fontes â†’ NarraÃ§Ã£o â†’ ComposiÃ§Ã£o â†’ Validar â†’ Render**

O nÂº de cenas Ã© **dinÃ¢mico** (sai do roteiro, range 4â€“12 de conteÃºdo + a CTA), o timing Ã© **fonte Ãºnica** (a duraÃ§Ã£o real de cada WAV deriva os `data-start/duration` e os tempos dos tweens), e o mesmo gerador escreve os dois formatos (`--vertical` para o 9:16, com safe zones e tÃ­tulo persistente de 2 linhas).

## Estrutura

```
skills/video-explicativo/
  SKILL.md                        # a skill: fluxo, plano de cenas, regras de ouro
  CHANGELOG.md                    # histÃ³rico de versÃµes (v1.yy.xxx)
  scripts/
    narration-template.sh         # gera os WAVs (inemavox chatterbox local, voz nei â†’ fallback Kokoro)
    fetch-fonts.mjs               # baixa Sora/Inter/JetBrains Mono como .woff2 local
    composition-template.mjs      # gerador data-driven (SCENES[] + CTA) â†’ index.html
  references/
    pipeline.md                   # pipeline detalhado (projeto, roteiro, timing, render)
    house-style.md                # paleta dark premium Ã¢mbar + tipografia
    motion.md                     # vocabulÃ¡rio de movimento M.* + catÃ¡logo de transiÃ§Ãµes
    safe-zones.md                 # zonas da UI dos apps no 9:16
    clips-midia.md                # vÃ­deo/imagem/mÃºsica, scrim/blur/painel, ducking
    revisao-texto.md              # checklist de acentuaÃ§Ã£o + lÃ©xico de inglÃªs fonÃ©tico
    gotchas.md                    # pegadinhas do HyperFrames
    sem-gpu.md                    # rodar sem GPU: edge-tts/Kokoro + imagens via Agnes AI
```

## Instalar

Copie a pasta da skill para as skills do Claude Code:

```bash
cp -r skills/video-explicativo ~/.claude/skills/
```

Depois Ã© sÃ³ pedir: *"faz um vÃ­deo explicativo sobre X"*.

## PrÃ©-requisitos

- Node 22+ e FFmpeg (no git-bash use `ffmpeg -nostdin`)
- Chrome headless do HyperFrames: `npx hyperframes browser ensure`
- TTS: inemavox local (engine `chatterbox`, voz `nei`; nunca `chatterbox-vc`); fallback Kokoro â€” `pip install kokoro-onnx soundfile`
- DiagnÃ³stico: `npx hyperframes doctor`

### Sem GPU?

O render nunca precisou de GPU â€” sÃ³ a narraÃ§Ã£o default (inemavox/chatterbox) e a geraÃ§Ã£o de
imagem local (flux2-klein) pedem. Substitutos diretos:

- **NarraÃ§Ã£o:** `edge-tts` (nuvem, sem chave) ou **Kokoro** (offline, CPU). Em ambos, **liste e
  escute as vozes antes** de gerar o vÃ­deo inteiro (`edge-tts --list-voices | grep pt-BR`;
  Kokoro PT: `pf_dora`, `pm_alex`, `pm_santa`).
- **Imagens:** **Agnes AI** via `~/projetos/imagens-agnes/gerar.py` (US$ 0, prompt em inglÃªs,
  `size` em pixels, mÃ¡x. 2 refs) â€” ou o **fallback SVG**, que dispensa rede e custo.

Receitas completas e regras medidas: [`references/sem-gpu.md`](skills/video-explicativo/references/sem-gpu.md).

## SaÃ­da

Todo o conteÃºdo de um vÃ­deo (projeto, assets, Ã¡udios, `index.html` e os MP4 finais) vive numa pasta Ãºnica: `~/projetos/output/<nome>/`. Um arquivo por formato â€” `<nome>-16x9.mp4` e `<nome>-9x16.mp4` â€” e sÃ³.

---

ConteÃºdo do [INEMA.CLUB](https://inema.club) Â· [PRO](https://inema.pro)

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>