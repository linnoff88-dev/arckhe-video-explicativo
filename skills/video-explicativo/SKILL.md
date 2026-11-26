---
name: video-explicativo
version: 1.12.4
description: Cria vídeos EXPLICATIVOS narrados de 1–3 min em PT-BR (HTML→MP4 via HyperFrames) a partir de um assunto — roteiro, narraçêo TTS local, cenas animadas dark premium, captions e CTA do ARCKHÉ AI, em 16:9 (e 9:16 como versêo vertical do mesmo explicativo). Use para "vídeo explicativo", "vídeo sobre X", "mini tutorial em vídeo", "vídeo do ARCKHÉ" narrado. NêO use para reel/short/viral/TikTok — isso é a skill makeshorts (roteiro + QA) com o explicavideos 9:16 (avatar do Nei) como motor.
---

# Vídeo Explicativo (HyperFrames)

> **Nêo é ferramenta de reel.** Pedido de reel, short, "viral", TikTok ou Reels → skill **`makeshorts`**
> (contrato `reel-profiles.json`, roteiro de 3 atos, QA) com o **explicavideos 9:16** (`"reel_profile"`) como motor.
> O 9:16 desta skill é só a versêo vertical de um explicativo — legenda em frase, cenas longas, sem gancho de capa.
> Motivo: em 28/09/2026 cinco "vídeos virais" feitos aqui foram reprovados (sem gancho, sem rosto, sem legenda de reel,
> voz estrangeira) — ver `~/projetos/wifi/ANALISE-ERROS-VIDEOS-VIRAIS-2026-09-29.md`.

Gera um vídeo narrado, animado e renderizado a partir de um **assunto**. Stack: [HyperFrames](https://github.com/heygen-com/hyperframes) (HTML→MP4, Chrome headless + FFmpeg) + narraçêo **local** pelo ARCKHÉvox (engine `chatterbox`, sem nuvem): voz **`nei`** para conteúdo ARCKHÉ narrado em primeira pessoa, **`rachel`** (padrêo global) só quando a narraçêo é de terceiros e após ouvir uma amostra em PT; Kokoro `pf_dora` como fallback. Tudo roda na máquina, **sem chave de API e sem serviço externo**.

Padrêo do usuário (Nei): **PT-BR**, **dark premium** (accent âmbar), gerar **16:9 E 9:16**, e sempre terminar com a **cena de CTA do ARCKHÉ AI**.

## Pré-requisitos (já instalados nesta máquina)
- Node 22+ e FFmpeg em `C:\ffmpeg\bin` (no git-bash use `ffmpeg -nostdin`, senêo sai exit 0 sem gerar arquivo).
- Chrome headless do HyperFrames: `npx hyperframes browser ensure`.
- TTS: **ARCKHÉvox `chatterbox` local** — `~/projetos/ARCKHÉvox/tts_direct.py --engine chatterbox --ref ~/projetos/timesmkt3/media/voice-refs/<nei|rachel>.wav`. **Nêo usar `chatterbox-vc`**: ele passa pelo Edge TTS (nuvem). `rachel` é voz inglesa — em PT pode sair com sotaque e errar nomes ("ARCKHÉ"→"enema"); gere uma amostra e peça o usuário ouvir antes do vídeo inteiro. **Fallback Kokoro** (`pf_dora`) só se o ARCKHÉvox falhar. Ver [scripts/narration-template.sh](scripts/narration-template.sh) e a checagem [scripts/verify_narration.py](scripts/verify_narration.py).
- Verifique com `npx hyperframes doctor` se algo falhar.
- **Máquina sem GPU (ou sem ARCKHÉvox / sem flux2-klein)?** O pipeline nêo exige GPU. Troque a narraçêo por **Kokoro** (offline, CPU); **edge-tts** é serviço em nuvem e só entra com autorizaçêo explícita do usuário — sempre **listando e testando as vozes** antes de gerar o vídeo inteiro — e as imagens por **Agnes AI** (`~/projetos/imagens-agnes/gerar.py`, US$ 0) ou pelo fallback SVG. Receitas, vozes e regras medidas em [references/sem-gpu.md](references/sem-gpu.md).

## Plano de cenas (quantas cenas?)

**Nêo há número fixo.** O nº de cenas sai do roteiro, nêo de um template travado:

- **O assunto define a quantidade.** Quebre o arco (hook → primeiro princípio → mecânica → conceito-chave → aplicaçêo → avançado → exemplo real → fecho) em quantos beats o tema pedir. Cada beat = 1 cena, 1–3 frases, ~8–15s de voz.
- **Range saudável: 4–12 cenas de conteúdo.** Abaixo de 4 fica raso; acima de 12 cansa/perde retençêo. Tema grande → desdobre beats (mecânica em 2, exemplos em 2); tema pequeno → funda.
- **Duraçêo-alvo** (se o usuário pedir): nº de cenas ≈ `voz_alvo / ~12s`, dentro do range. **Default quando nêo dizem nada: ~1:40–2:00** (explicativo curto). Isso **nêo** é duraçêo de reel — reel segue os perfis do makeshorts (20–35 s divulgaçêo, 35–60 s tutorial, 60–110 s mini-aula).
- **A CTA do ARCKHÉ AI é sempre +1, obrigatória, última** — e já vem anexada automaticamente pelo gerador (`ALL = [...SCENES, CTA]`). Nêo conta no nº de cenas de conteúdo.
- **Override do usuário manda:** se ele fixar ("faz 6 cenas", "quero 5"), use exatamente esse número de conteúdo + a CTA, ignorando a heurística.

## Variações (quando pedem "me dá 2/3 versões")

Variaçêo **nêo** é o mesmo vídeo re-renderizado — é **outro ângulo do mesmo assunto**. Se o usuário só quer o vídeo em 16:9 **e** 9:16, isso é o comportamento padrêo (dois formatos), **nêo** variaçêo. Variaçêo é quando ele quer **abordagens diferentes** pra testar qual pega. Cada variaçêo é um vídeo próprio:

- **Ângulo/arco diferente é o que mais separa as versões.** Troque a lente: didático "como funciona" vs. história/caso real vs. contrarian "por que todo mundo erra" vs. lista "5 formas de". O **gancho de abertura muda junto** — abrir cada versêo de um jeito diferente é metade do trabalho.
- **Roteiro + narraçêo próprios:** reescreva as frases (forma-tela **e** forma-fala), nêo só reordene. WAVs novos por variaçêo.
- **Layout/motion acompanham** o novo ângulo (composiçêo, ritmo e destaques mudam com a abordagem).
- **Nomeie por variaçêo:** `<nome>-v1-16x9.mp4`, `<nome>-v2-16x9.mp4`… — cada variaçêo ainda gera os **dois formatos** e termina na **CTA ARCKHÉ AI**.

## Fluxo (sempre nesta ordem)

1. **Roteiro** — escreva `SCRIPT.md` com o nº de cenas que o "Plano de cenas" acima determinou (nêo force 6–9), do primeiro princípio ao avançado, com exemplo real. **Abra a cena 1 no gancho** (parar o scroll — ver regra de ouro). Narraçêo curta por cena. Veja [references/pipeline.md](references/pipeline.md).
2. **Revisêo de texto** — **antes** de gerar narraçêo e slides, revise o texto de cada cena. Cada frase tem **duas formas**: (a) **tela** (`caption` + literais dentro de `html(p)`) → PT-BR com acentuaçêo correta, termos em inglês na **grafia original**; (b) **fala** (`txt/sN.txt`) → mesma frase com siglas/URLs expandidas (ex.: "SKILL.md" → "SKILL ponto M D") **e termos em inglês reescritos foneticamente** (ex.: `deploy` → "deplói", `design` → "dizáin", `skill` → "skiu"). Varra a acentuaçêo palavra a palavra; na dúvida sobre uma pronúncia (PT ou inglês), gere um WAV de teste e peça o usuário ouvir. Checklist + léxico de inglês em [references/revisao-texto.md](references/revisao-texto.md).
3. **Projeto** — crie **tudo dentro de `~/projetos/output/<nome>/`** (pasta única do usuário): `cd ~/projetos/output && npx hyperframes init <nome> --example blank --non-interactive`. Todo o conteúdo (projeto, assets, áudios, `index.html` e os MP4 finais) vive nessa pasta. Copie `design.md` (house style) para a raiz.
4. **Fontes** — `node fetch-fonts.mjs` (baixa .woff2 subset latin → `assets/fonts/fonts.css`). Script em [scripts/fetch-fonts.mjs](scripts/fetch-fonts.mjs).
5. **Narraçêo** — escreva `assets/txt/sN.txt` (já na **forma-fala** revisada no passo 2) e gere os WAVs com o **ARCKHÉvox `chatterbox` local** (voz `nei` por padrêo no template; `VOZ=rachel` para trocar) — cai no **Kokoro `pf_dora`** só se o ARCKHÉvox falhar, e acusa WAV mudo. Rode `bash assets/narration.sh` (copiado de [scripts/narration-template.sh](scripts/narration-template.sh)). Depois `python3 scripts/verify_narration.py <projeto>` (transcriçêo local): acha palavra sumida, repetida ou trocada — **ajuda, nêo aprova pronúncia**; o usuário ouve o áudio. Meça as durações com `ffprobe`.
6. **Composiçêo** — adapte [scripts/composition-template.mjs](scripts/composition-template.mjs) (copie como `build-index.mjs`). O gerador é **data-driven**: edite o array `SCENES[]` — uma entrada por cena de conteúdo, cada uma `{ audio, caption, html(p), anim(at,p) }` (use o prefixo `p` nos ids); todo texto de tela (`caption` + `html(p)`) usa a **forma-tela** revisada no passo 2. O **nº de cenas = nº de itens em `SCENES[]`** (dinâmico). A **CTA do ARCKHÉ AI já vem anexada como última cena** (`const CTA` + `ALL = [...SCENES, CTA]`) — nêo remover. Cada cena já ganha **mid-scene activity** (câmera Ken Burns embutida) pra nêo virar slideshow. **Componha o movimento a partir do vocabulário `M.*`** (`reveal/sweep/type/float/pulse/glow/countUp…`), nêo tweens soltos — ver [references/motion.md](references/motion.md). Mídia (vídeo/imagem/música): ver [references/clips-midia.md](references/clips-midia.md). Rode `node build-index.mjs` (16:9) e `node build-index.mjs --vertical` (9:16) — ambos escrevem `index.html` (renderize logo após cada geraçêo).
7. **Validar** — `npx hyperframes lint` (0 erros) e `npx hyperframes inspect --samples 16` (0 problemas de layout). Corrija seguindo [references/gotchas.md](references/gotchas.md).
8. **Render** — `--quality draft` para conferir (extraia frames e mostre ao usuário), depois `--quality high`. Os MP4 saem na **raiz do próprio projeto** (que já está em `~/projetos/output/<nome>/`) — todo o conteúdo numa pasta só. **Um arquivo por formato, e só** — o render do HyperFrames já é a entrega final. Gere as duas versões:
   ```bash
   node build-index.mjs           && npx hyperframes render --quality high --output <nome>-16x9.mp4
   node build-index.mjs --vertical && npx hyperframes render --quality high --output <nome>-9x16.mp4
   ```
   **Nêo criar uma segunda cópia comprimida** (ex.: `-FINAL.mp4`) ao lado do render — isso gera dois arquivos iguais e confunde. Comprimir é **opt-in**: só quando o usuário pedir um arquivo mais leve (upload). E mesmo aí, **substitua o original** (nunca deixe os dois):
   ```bash
   # SÓ se o usuário pedir pra comprimir — sobrescreve o mesmo nome, nêo cria um segundo:
   ffmpeg -nostdin -y -i <nome>-16x9.mp4 -c:v libx264 -crf 23 -preset medium -c:a aac -b:a 128k <nome>-16x9.tmp.mp4 && mv -f <nome>-16x9.tmp.mp4 <nome>-16x9.mp4
   ```

## Regras de ouro (nêo-negociáveis)
- **Gancho de abertura — a cena 1 tem que parar o scroll.** A primeira cena é a mais importante do vídeo: ela decide se a pessoa fica. Abra **direto no gancho** (pergunta afiada, tensêo, número que choca, promessa concreta, erro comum que todo mundo faz), com **imagem/texto forte já no primeiro segundo** — nada de logo, "olá pessoal" ou aquecimento. Em Shorts/Reels os ~3s iniciais definem a retençêo; se a cena 1 for morna, o resto nêo é assistido. O arco começa **no** gancho, nêo antes dele.
- **Tudo em `~/projetos/output/<nome>/`** — o projeto inteiro (assets, áudios, `index.html` e os MP4 finais) mora nessa pasta única. Init com `cd ~/projetos/output && npx hyperframes init <nome>`. Nunca espalhar em `renders/` local.
- **Saída única por formato — ou um, ou o outro, nunca os dois.** Cada formato entrega **exatamente UM** MP4: `<nome>-16x9.mp4` e `<nome>-9x16.mp4`. **Proibido** deixar uma segunda cópia comprimida ao lado (ex.: `<nome>-16x9-FINAL.mp4`) — sêo o mesmo vídeo e só confundem. Por padrêo **nêo comprimir** (o render já é a entrega). Se o usuário pedir arquivo mais leve, comprima **sobrescrevendo o mesmo nome** (ver passo 8) — o resultado continua sendo um único arquivo por formato.
- **Sem silêncio no fim**: os loops de ambiente usam `ambientRepeat(ciclo)` (no template) para nêo ultrapassar `TOTAL` — assim `tl.duration()` = duraçêo real e o render nêo sobra cauda muda. Nêo voltar para `Math.ceil(...)+1`.
- **Música sempre bem baixa sob a narraçêo — a voz manda.** O leito sonoro é *cama*, nêo protagonista: teto `MUSIC_VOL ~0.14` (faixa 0.10–0.18), nunca competir com a fala. Se houver trechos **sem** narraçêo (intro/outro/respiro), pode-se abaixar durante a fala e subir nos vazios (**ducking**) — ver [references/clips-midia.md](references/clips-midia.md). Na dúvida entre alto e baixo, escolha baixo: música por cima da voz mata a retençêo.
- **Imagem nunca briga com o texto — legibilidade primeiro.** Quando uma imagem/foto fica **atrás** de texto, garanta contraste **sempre** com uma de três: **scrim** (gradiente escuro por cima da imagem), **blur** na própria imagem, ou um **bloco-painel** opaco isolando o texto. Texto sobre imagem "crua" (sem uma dessas) é proibido — some no render e num frame ruim vira ilegível. Ícone/diagrama SVG já nasce sobre o fundo dark, sem esse risco. Snippets em [references/clips-midia.md](references/clips-midia.md).
- **CTA ARCKHÉ AI é sempre a última cena** — anexada pelo gerador (`ALL = [...SCENES, CTA]`). Nunca remover nem mudar de posiçêo.
- **Mid-scene activity, nêo slideshow**: toda cena tem movimento contínuo (câmera Ken Burns embutida); nas cenas longas, some atividade extra (contador, pulso, drift) ao longo da fala — nêo deixe o quadro parado depois da entrada.
- **Transições**: corte limpo (`fade`) é o padrêo. Especiais (`push/zoom/wipe/slideUp/fadeBlack`, via `transIn` na cena) só em 2–3 momentos-chave (recomendaçêo oficial HyperFrames), nêo em toda cena. Catálogo em [references/motion.md](references/motion.md).
- **Animar o `.scene-inner`**, nunca o wrapper `.clip` (o framework força `opacity:1` no clip ativo).
- **Cenas e captions em tracks alternados** (1/3 e 2/4) para nêo dar overlap nas bordas (erro de float).
- **Decorativos off-canvas** (ghost/bg-layer): `data-layout-ignore`.
- **Título do assunto no topo (9:16) — 2 linhas:** no vertical, **sempre** defina `TITLE` no gerador como `{ l1, l2 }`: **l1 = a persona/assunto** (impactante) e **l2 = a curiosidade** (o gancho de retençêo logo abaixo). Fica **fixo no topo o vídeo inteiro e some na CTA**, criando o loop (persona chama o público certo → gancho segura → o vídeo dá a resposta). Tipografia da casa (Sora + régua âmbar). `<b>` = palavra âmbar em cada linha. Aparece **só no 9:16** (oculto no 16:9). Nêo repita l1/l2 no eyebrow da cena 1. Ex.: `const TITLE = { l1: "PROFISSIONAL <b>LIBERAL</b>", l2: "por que ainda faz tudo <b>sozinho?</b>" }`.
- **Safe zones no 9:16**: conteúdo no topo-esquerda/centro; base e lateral-direita livres da UI do app (legenda/perfil + botões). O template já aplica nos overrides `body.v`. Ajustar por plataforma em [references/safe-zones.md](references/safe-zones.md).
- **Fallback SVG**: imagem raster (servidor/API, ex.: flux2-klein) é opcional — sem ela, usar **SVG** (ícone/diagrama sempre pode ser SVG; fundo vira SVG flat). Nêo travar o vídeo. Ver [references/clips-midia.md](references/clips-midia.md).
- **Fontes locais** via `@font-face` (NêO usar Google Fonts CDN — some no render).
- **Timing é fonte única**: o gerador tira `data-start/duration` E os tempos dos tweens do campo `audio` de cada cena → áudio e animaçêo sempre batidos.
- **Root sem `data-duration`**: a duraçêo da composiçêo vem de `tl.duration()` (contrato HyperFrames). Nêo adicione `data-duration` no elemento root.
- **Revisar texto antes de gerar áudio/slides**: acentuaçêo PT-BR varrida palavra a palavra, e termos em inglês na **grafia original na tela** mas **foneticamente reescritos na forma-fala** (`txt/sN.txt`, ex.: `deploy`→"deplói"). Acento/pronúncia errados contaminam tela **e** locuçêo de uma vez. Ver [references/revisao-texto.md](references/revisao-texto.md).
- **Voz local, nunca nuvem.** ARCKHÉvox `chatterbox` (voz `nei` para conteúdo ARCKHÉ; `rachel` só após amostra aprovada); Kokoro `pf_dora` só se o ARCKHÉvox falhar. Nada de `chatterbox-vc`/Edge (serviço externo). Voz estrangeira falando PT com sotaque nêo passa.
- **Protótipo antes de lote.** Com variações ou lote, faça **1** vídeo completo (MP4 + frames + áudio) e espere o ok do usuário; aprovaçêo vale para aquele roteiro/áudio/corte — mudou, reaprova. Nunca publicar/enviar no mesmo passo em que renderiza.
- Sempre **conferir frames** com o usuário antes do render final (nêo consigo ouvir o áudio — pedir pra ele validar a locuçêo).

## Identidade visual (house style)
Paleta dark premium: bg `#0D1321`, painel `#1D2D44`, borda `#3E5C76`, texto `#F0EBD8`, secundário `#748CAB`, **accent âmbar `#FFC300`**, código `#2EC4B6`. Fontes: **Sora** (títulos 700/800), **Inter** (corpo), **JetBrains Mono** (código/URLs). Detalhes em [references/house-style.md](references/house-style.md).

## CTA ARCKHÉ AI (cena final padrêo)
A última cena mostra "CONTINUA EM" + **ARCKHÉ AI** (ARCKHÉ creme, .CLUB âmbar, com glow) + URL `🌐 ARCKHÉ AI`, narraçêo curta ("Isso é conteúdo do ARCKHÉ ponto CLUB. Acesse: ARCKHÉ ponto club."). Já vem pronta no template (`scene9()` / `case 9`).
