<div align="center">

<img src="docs/media/hero.svg" alt="Whispering Eaves — a curtain of glyphs hanging from a palace eave, swinging like cloth after a swipe" width="100%">

<p>
  <a href="https://whispering-eaves.vercel.app/"><img alt="Live demo" src="https://img.shields.io/badge/live%20demo-enter%20the%20exhibition-e0533f?style=for-the-badge&labelColor=120d0b"></a>
  <img alt="Canvas" src="https://img.shields.io/badge/Canvas-Verlet%20cloth-d9a441?style=for-the-badge&labelColor=120d0b">
  <img alt="Web Audio" src="https://img.shields.io/badge/Web%20Audio-pentatonic-4a8fb3?style=for-the-badge&labelColor=120d0b">
  <img alt="Dependencies" src="https://img.shields.io/badge/dependencies-none-e0533f?style=for-the-badge&labelColor=120d0b">
</p>

<p><em>听檐 · 囁く庇 · Сыбырлаған Шатырлар · Fısıldayan Saçaklar<br>
A full-screen web artwork where curtains of text hang from historic eaves and move like real cloth.</em></p>

<a href="https://whispering-eaves.vercel.app/"><img src="shots/01_china_default.png" alt="The Xi'an scene: golden palace eaves with a curtain of Chinese characters" width="100%"></a>

</div>

<img src="docs/media/divider.svg" width="100%" alt="">

## The exhibition

Whispering Eaves fuses architectural heritage with real-time cloth physics. Inspired by building eaves and traditional text curtains (*noren*, *fengling*), it takes you on a journey across five cultures of the ancient Silk Road. Swipe the curtain and the characters part, stretch and scatter, then spring back.

It is written in plain HTML5 Canvas, CSS and the Web Audio API: no build step, no npm packages and no frameworks.

| # | Destination | Eaves | Script | Sound |
|---|---|---|---|---|
| **01** | China · Xi'an | Golden palace eaves and interlocking dougong brackets | Classical Chinese characters | Guzheng pentatonic zither |
| **02** | Japan · Nara | Vermilion shrine roof, hip-and-gable irimoya | Kanji and hiragana | Koto scale |
| **03** | Kazakhstan · Altay | Felt yurt and the shanyrak sky ring | Kazakh Cyrillic and Turkic runes | Dombra twin-string folk scale |
| **04** | Uzbekistan · Samarkand | Registan iwan and cobalt glazed tiles | Uzbek calligraphy and motifs | Oud and sitar scale |
| **05** | Türkiye · Istanbul | Bosphorus mansion and lead-clad dome | Ottoman Turkish and rumi motifs | Kanun and ney melodies |

<table>
  <tr>
    <td width="33%"><img src="shots/07_japan_settled.png" alt="Nara: vermilion shrine eaves"></td>
    <td width="33%"><img src="shots/09_kazakhstan.png" alt="Altay: the yurt and shanyrak"></td>
    <td width="33%"><img src="shots/10_gallery.png" alt="The gallery of destinations"></td>
  </tr>
  <tr>
    <td align="center"><code>02</code> · Nara</td>
    <td align="center"><code>03</code> · Altay</td>
    <td align="center"><code>gallery</code> · the five stops</td>
  </tr>
</table>

<img src="docs/media/divider.svg" width="100%" alt="">

## What you can do

| | |
|---|---|
| **Text curtain** | A Verlet grid of mass points and distance constraints. Swipes part, rotate and scatter the glyphs; type your own text and a new curtain is woven live. |
| **Artifact inspector** | Wireframe 3D artifacts from each culture, rotated with the pointer. |
| **Poetry recitation** | Original poems with speech-synthesis voiceovers. |
| **Keyboard instrument** | Regional chime notes that send physical waves across the curtain. |
| **Silk Road map** | Caravan routes from Xi'an to Istanbul, with nodes that teleport you to each stop. |
| **Seasons and weather** | A weather engine that changes the mood of every scene. |
| **Poster export** | The current frame, paper texture, roof artwork and typography as a 1848 × 1080 PNG. |

| Key | Action |
|---|---|
| <kbd>Z</kbd> | Zen mode: hide the interface |
| <kbd>1</kbd> – <kbd>9</kbd> | Pentatonic notes, left to right |
| <kbd>A</kbd> – <kbd>K</kbd> | Atmospheric chimes |
| <kbd>Esc</kbd> | Close a modal or leave the gallery |

<img src="docs/media/divider.svg" width="100%" alt="">

## How the cloth moves

Every curtain is a grid of point masses: 25 to 27 chains of 40 points, each chain pinned under the eave, each point carrying one glyph. The simulation runs every frame with the time step capped at 1/30 s:

| Step | What happens |
|---|---|
| **1 · Integrate** | Verlet: velocity is the last move times a damping of 0.982, plus gravity of 1500 px/s² in design space. |
| **2 · Pointer** | Only while the pointer is actually moving (above 30 px/s): points within a 165 px radius are pushed by the smoothed pointer velocity, clamped to 2400 px/s. Resting a finger does nothing. |
| **3 · Constraints ×4** | Vertical neighbours are projected back to their rest length at full stiffness; weak horizontal shear springs (0.045) keep strands loosely together without turning the curtain into a sheet. |
| **4 · Breeze** | A barely perceptible idle sway, held off for the first two seconds after a scene arrives. |
| **5 · Clamp** | Point speed is capped at 1100 px/s so a hard flick cannot explode the grid. |

Each glyph is drawn from a sprite atlas rendered once per scene at 3× and rotated to follow the line between its neighbours, so the characters lean as the strand bends. There is no `fillText` per frame.

<table>
  <tr>
    <td width="33%"><img src="shots/02_swipe_mid.png" alt="Mid-swipe: strands parting"></td>
    <td width="33%"><img src="shots/04_swipe_after_2s.png" alt="Two seconds later: the curtain swinging back"></td>
    <td width="33%"><img src="shots/06_trans_empty_beat.png" alt="The empty paper beat between scenes"></td>
  </tr>
  <tr>
    <td align="center">mid-swipe</td>
    <td align="center">+2 s · swinging back</td>
    <td align="center">the empty beat between stops</td>
  </tr>
</table>

<img src="docs/media/divider.svg" width="100%" alt="">

## Between destinations

A scene change is a small piece of choreography: the building slides out (0.38 s), the paper is left empty for a 400 ms beat, then the next building slides in with a slight overshoot and its curtain starts perfectly still. Text swaps while it is invisible, the URL hash updates so every stop is linkable, and the curtain ignores input until it has arrived. Only the active curtain is simulated; a leaving one keeps rendering with physics frozen and is released once it fades out.

| Detail | Choice |
|---|---|
| Frame loop | `requestAnimationFrame` with a clamped real `dt`; pauses when the tab is hidden |
| Resolution | Device pixel ratio capped at 2 |
| Shadows | Recomposited only when something moved; otherwise the last frame is reused |
| Input | Pointer Events with `touch-action: none`; one tracked pointer, mouse and touch alike |
| Idle | After 9 s without input, a soft invisible hand sweeps the curtain every ~7 s; any real touch cancels it |
| Accessibility | `prefers-reduced-motion` turns off the breeze and the demo sweep |

<img src="docs/media/divider.svg" width="100%" alt="">

## Türkçe özet

**Fısıldayan Saçaklar (Whispering Eaves)**, tarihi saçaklardan sarkan yazı perdelerini gerçek kumaş fiziğiyle hareket ettiren, tam ekran bir web sanat eseri. İpek Yolu boyunca beş durak var: Şian, Nara, Altay, Semerkant ve İstanbul. Her durağın kendi mimarisi, yazısı ve pentatonik müziği var. Perdeyi kaydırınca harfler dağılıp esnek biçimde geri toplanıyor. Kendi metninle perde örebilir, klavyeyle nota çalabilir, sahneyi poster olarak indirebilirsin. Derleme adımı ya da kütüphane yok; tek bir HTML dosyası.

<img src="docs/media/divider.svg" width="100%" alt="">

## Running locally

```sh
python3 -m http.server 8080 --directory silk-atlas
# http://localhost:8080
```

```
silk-atlas/      the artwork: index.html and its roof, artifact and texture assets
assets-src/      source artwork before optimisation
tests/           Playwright probes for physics, transitions and images (Python)
shots/           screenshots from the test runs
```

<div align="center">
<br>
<a href="https://github.com/kutluhangil"><img src="docs/media/signature.svg" width="100%" alt="Built by kutluhangil"></a>
<sub>Five cultures, one curtain · every poem, motif and melody written for this piece · the banner is an illustration of the curtain.</sub>
</div>
