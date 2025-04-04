---
display_section: posts
---

<style>
  p {
text-indent: 2em;
text-justify: inter-ideograph;
text-align: left;
font-size: 0.875em;
  }

  .tomb img {
cursor: pointer;
transition: transform 0.6s ease;
transform-style: preserve-3d;
  }

  /* ✅ 翻转容器 */
  .flip-container {
width: 18em;
height: 28em;
margin: 1em auto;
perspective: 1000px;
cursor: pointer;
  }

  .flipper {
position: relative;
width: 100%;
height: 100%;
transition: transform 1s;
transform-style: preserve-3d;
  }

  .flipped .flipper {
transform: rotateY(180deg);
  }

  .flip-face {
position: absolute;
width: 100%;
height: 100%;
backface-visibility: hidden;
  }

  .back {
transform: rotateY(180deg);
  }

  /* ✅ 墓碑样式（通用） */
  .special-caption {
width: 18em;
height: 30em;
padding: 0.4em 1em 1em 1em;
box-sizing: border-box;
background:
  linear-gradient(135deg, #dcdcdc, #c2c2c2),
  url('https://www.transparenttextures.com/patterns/subtle-grunge.png');
background-blend-mode: multiply;
background-size: cover;
border: 1px solid #aaa;
border-radius: 5px;
box-shadow:
  inset 0 0 4px rgba(0, 0, 0, 0.3),
  1px 1px 3px rgba(0, 0, 0, 0.15);

font-family: "Source Han Serif SC", "Noto Serif CJK SC", serif;
font-size: 0.75rem;
color: #551414;
letter-spacing: 0em;
line-height: 1.5;
font-style: normal;
user-select: none;
position: relative;
  }

.special-caption::after {
  content: "";
  position: absolute;
  right: -6px;
  bottom: -6px;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.08);
  transform: skew(-3deg, -3deg);
  z-index: -1;
  border-radius: 5px;
}

  /* ✅ 正文左对齐 */
  .special-caption.left-align {
display: flex;
flex-direction: column;
align-items: flex-start;
justify-content: flex-start;
text-align: left;
  }

  /* ✅ 竖排样式 */
  .special-caption.vertical {
writing-mode: vertical-rl;
text-orientation: upright;
white-space: nowrap;
display: flex;
justify-content: center;
align-items: center;
text-align: center;
  }

  .caption-inner {
font-size: 3rem;
letter-spacing: 0.2em;
line-height: 1.8;
  }

  .caption-title {
font-weight: bold;
font-size: 1.5em;
margin-bottom: 1em;
color: #5a0e0e;
text-align: center;
width: fit-content;
margin-left: auto;
margin-right: auto;
  }

  .caption-end {
font-size: 1em;
margin-top: 1.5em;
color: #5a0e0e;
opacity: 0.85;
text-align: center;
width: fit-content;
margin-left: auto;
margin-right: auto;
  }

</style>

<!-- ✅ 图像 + 翻转墓碑容器 -->
<span class="tomb">
  <figure>
<img id="switchImage" src="https://s2.loli.net/2024/09/24/loMhr7yZzc54uXm.jpg" width="90%">
<figcaption>
  <div id="flipBox" class="flip-container">
<div class="flipper">

  <!-- 正面 -->
  <div class="flip-face front">
<div class="special-caption left-align">
<div style="margin-top: 0; margin-bottom: 0.3em; font-size: 0.65em; color: #444; text-align: center; width: 100%;">
  曾建于 ? 年 ? 月 ? 日 · 原立于 Polyterrasse
</div>
  <div class="caption-title">斐康明之墓</div>
  <div>此地无声，文字长存。</div>
  <div>留此坟，纪念一介无用学人。</div>
  <div>所藏多抄录课文，或记浮生琐屑。</div>
  <div>自谓为文，实则絮语。</div>
  <div><a href="/tags">标签</a>纷陈，可窥一斑；</div>
  <div>手抄之劳，聊胜于无。</div>

  <div style="margin-top: 1em;">读者寥寥，实乃无趣之地。</div>
  <div>若有疑问，可<a href="mailto:young-mann1043@outlook.com">焚纸于此</a>传意。</div>
  <div>吾愿与君，比划一番。⚔️</div>

  <div class="caption-end">— 自撰此铭，以备来者 —</div>
</div>

  </div>

  <!-- 反面 -->
  <div class="flip-face back">
<div class="special-caption vertical">
  <div class="caption-inner">后步宽宏</div>
</div>
  </div>

</div>
  </div>
</figcaption>
  </figure>

   <!-- 正面你好呀，这里是 [我](../posts/about) 的电子坟头，存放的多是从课本上抄来的内容，亦或是我线下生活的流水账，从这些 [标签](../tags) 可窥见一斑。读起来大概挺无趣的。

  有事可以 [发邮件给我](mailto:young-mann1043@outlook.com)，让咱俩好好比划比划⚔️！ -->
</span>

<script>
  const flipBox = document.getElementById("flipBox");
  const imgElement = document.getElementById("switchImage");

  let hasLoadedSnow = false;

  function toggleFlip() {
flipBox.classList.toggle("flipped");

// 只在第一次触发时加载雪花
if (!hasLoadedSnow) {
  loadSnowScript({
"data-num": "1",
"data-interval": "2500",
"data-speed": "16",
"data-snow": "🍫",
"data-color": "black"
  });
  loadSnowScript({
"data-num": "3",
"data-interval": "1000",
"data-speed": "10",
"data-snow": "❅, ❆, ❈,❊, ✻",
"data-color": "black"
  });
  hasLoadedSnow = true;
}
  }

  // 点击图片或墓碑任意处翻转
  flipBox.addEventListener("click", function (e) {
  // 如果点击的是链接，不触发翻转
  if (e.target.tagName.toLowerCase() === "a") {
    e.stopPropagation(); // 阻止事件冒泡（可选）
    return;
  }
  toggleFlip();
});

  imgElement.onclick = toggleFlip;

  function loadSnowScript(attrs) {
const script = document.createElement("script");
script.src = "https://mengrru.github.io/magic-conch/snow/index.js";
for (const [key, val] of Object.entries(attrs)) {
  script.setAttribute(key, val);
}
document.body.appendChild(script);
  }
</script>
