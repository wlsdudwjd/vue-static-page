<template>
  <main class="page" role="main" aria-label="한가위 테마 랜딩">
    <!-- 배경 장식 -->
    <div class="sky" aria-hidden="true">
      <div class="stars"></div>
      <div class="moon">
        <div class="moon-face"></div>
      </div>
      <div class="cloud cloud-a"></div>
      <div class="cloud cloud-b"></div>
      <div class="cloud cloud-c"></div>
      <div class="hills"></div>
    </div>

    <!-- 콘텐츠 -->
    <section class="hero" aria-labelledby="headline">
      <header class="headline-wrap">
        <h1 id="headline" class="headline">
          풍성한 한가위<span class="sep">,</span> <span class="accent">보름달처럼</span> 가득 채우세요
        </h1>
        <p class="sub">
          {{ currentWish }}
        </p>
        <div class="cta-row">
          <button class="btn primary" @click="shuffleWish">복 빌기</button>
          <a class="btn ghost" href="#" @click.prevent="toggleLanterns">
            {{ lanternsOn ? '연등 끄기' : '연등 켜기' }}
          </a>
        </div>
      </header>

      <!-- 연등 -->
      <ul class="lanterns" v-if="lanternsOn" aria-label="하늘에 떠 있는 연등">
        <li
            v-for="l in lanternList"
            :key="l.id"
            class="lantern"
            :style="{
            left: l.left,
            animationDuration: l.duration + 's',
            animationDelay: l.delay + 's',
            filter: l.warm ? 'saturate(1.15)' : 'saturate(0.95)'
          }"
            aria-hidden="true"
        >
          <div class="lantern-body"></div>
          <div class="lantern-light"></div>
          <div class="lantern-tail"></div>
        </li>
      </ul>
    </section>

    <!-- 정보 섹션 -->
    <section class="info" aria-label="추석 소개">
      <div class="card">
        <h2 class="card-title">한가위(추석)</h2>
        <p class="card-text">
          한가위는 음력 8월 15일로, 올 한 해 수확에 감사하며 보름달을 보며 소원을 비는 날이에요.
          송편을 빚고, 보름달에 감사와 소망을 전해 보세요.
        </p>
        <ul class="chips">
          <li class="chip">#보름달</li>
          <li class="chip">#송편</li>
          <li class="chip">#풍년</li>
          <li class="chip">#가족</li>
        </ul>
      </div>
    </section>

    <footer class="footer" aria-label="바닥글">
      <p>© {{ year }} Hangawi — 만든이: 당신 ✨</p>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const year = new Date().getFullYear()

const wishes = [
  '가족 모두 건강하고 평안하세요',
  '달처럼 환한 행운이 가득하길!',
  '웃음이 집안 가득, 근심은 멀리 ✨',
  '소원 성취! 하는 일마다 술술~',
  '보름달 보고 빌면 다 이뤄져요'
]
const currentWish = ref(wishes[0])

function shuffleWish() {
  const idx = Math.floor(Math.random() * wishes.length)
  currentWish.value = wishes[idx]
}

type Lantern = {
  id: number
  left: string       // css %
  duration: number   // seconds
  delay: number      // seconds
  warm: boolean
}

const lanternsOn = ref(true)
const lanternList = ref<Lantern[]>([])

function spawnLanterns(count = 10) {
  const arr: Lantern[] = []
  for (let i = 0; i < count; i++) {
    arr.push({
      id: i,
      left: `${Math.floor(Math.random() * 100)}%`,
      duration: 18 + Math.random() * 14, // 18s ~ 32s
      delay: Math.random() * 8,
      warm: Math.random() > 0.5
    })
  }
  lanternList.value = arr
}

function toggleLanterns() {
  lanternsOn.value = !lanternsOn.value
}

let wishTimer: number | undefined

onMounted(() => {
  spawnLanterns(14)
  wishTimer = window.setInterval(shuffleWish, 3500)
})

onUnmounted(() => {
  if (wishTimer) window.clearInterval(wishTimer)
})
</script>

<style>
/* ====== 글로벌 리셋 (좌우 여백 제거) ====== */
*,
*::before,
*::after {
  box-sizing: border-box;
}
html, body, #app {
  height: 100%;
  margin: 0;
  padding: 0;            /* 좌우 여백 완전 제거 */
}
body {
  font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Apple SD Gothic Neo, Noto Sans KR, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", sans-serif;
  color: #f6f7fb;
  background: #0a0f1c;
}

/* ====== 레이아웃 ====== */
.page {
  min-height: 100svh;
  width: 100vw;         /* 뷰포트 가로 꽉 차게 */
  overflow-x: hidden;   /* 데코가 넘칠 때 가로 스크롤 방지 */
  display: grid;
  grid-template-rows: auto auto 1fr auto;
}

.hero {
  position: relative;
  min-height: 72svh;
  display: grid;
  place-items: center;
  padding: clamp(16px, 4vw, 40px) 16px;
  text-align: center;
  isolation: isolate;
}

.info {
  position: relative;
  z-index: 1;
  padding: 24px clamp(16px, 5vw, 48px) 40px;
}

.footer {
  text-align: center;
  padding: 18px 12px 28px;
  opacity: 0.85;
  font-size: 0.9rem;
}

/* ====== 배경 요소 ====== */
.sky {
  position: fixed;
  inset: 0;
  z-index: 0;
  overflow: hidden;
  background: radial-gradient(1200px 800px at 70% 10%, #1a1f35 0%, #0b1021 40%, #070b18 100%);
}

.stars,
.stars::before,
.stars::after {
  content: "";
  position: absolute;
  inset: 0;
  background-image:
      radial-gradient(2px 2px at 20% 30%, rgba(255,255,255,.9), transparent 60%),
      radial-gradient(1.5px 1.5px at 80% 20%, rgba(255,255,255,.65), transparent 60%),
      radial-gradient(1.5px 1.5px at 10% 70%, rgba(255,255,255,.7), transparent 60%),
      radial-gradient(1px 1px at 55% 55%, rgba(255,255,255,.5), transparent 60%),
      radial-gradient(1px 1px at 40% 85%, rgba(255,255,255,.45), transparent 60%);
  animation: twinkle 9s linear infinite;
  opacity: 0.9;
}
.stars::before { animation-duration: 12s; opacity: .6; }
.stars::after { animation-duration: 15s; opacity: .4; }

@keyframes twinkle {
  0%, 100% { transform: translateY(0px); }
  50% { transform: translateY(-6px); }
}

.moon {
  position: absolute;
  top: min(10vh, 80px);
  right: clamp(12px, 4vw, 64px);
  width: clamp(120px, 18vw, 220px);
  aspect-ratio: 1/1;
  border-radius: 50%;
  background:
      radial-gradient(circle at 35% 35%, #fff7d6 0%, #ffeaa8 35%, #ffd978 58%, #f6c04f 72%, #e2a93b 100%);
  box-shadow:
      0 0 24px 8px rgba(255, 229, 134, .35),
      0 0 120px 24px rgba(255, 226, 140, .18);
  filter: saturate(1.05);
}
.moon-face {
  position: absolute;
  inset: 0;
  background:
      radial-gradient(18% 18% at 50% 55%, rgba(0,0,0,.06), transparent 60%),
      radial-gradient(12% 12% at 62% 42%, rgba(0,0,0,.08), transparent 60%),
      radial-gradient(14% 14% at 38% 40%, rgba(0,0,0,.05), transparent 60%);
  border-radius: 50%;
  mix-blend-mode: multiply;
  opacity: .7;
}

.cloud {
  position: absolute;
  top: 18vh;
  width: clamp(220px, 35vw, 520px);
  height: 120px;
  background:
      radial-gradient(closest-side, rgba(255,255,255,.08), transparent) 20% 60%/40% 80% no-repeat,
      radial-gradient(closest-side, rgba(255,255,255,.1), transparent) 50% 50%/60% 90% no-repeat,
      radial-gradient(closest-side, rgba(255,255,255,.08), transparent) 80% 60%/40% 70% no-repeat;
  filter: blur(2px);
  opacity: .6;
  animation: drift 60s linear infinite;
}
.cloud-a { left: -30%; top: 24vh; animation-duration: 64s; }
.cloud-b { left: -50%; top: 32vh; animation-duration: 72s; }
.cloud-c { left: -20%; top: 18vh; animation-duration: 88s; }

@keyframes drift {
  0% { transform: translateX(0); }
  100% { transform: translateX(160%); }
}

.hills {
  position: absolute;
  left: 0; right: 0; bottom: -2px;
  height: 28svh;
  background:
      radial-gradient(80% 60% at 50% 120%, #0e1327 0%, #0a0f1c 60%, transparent 61%) bottom center / 120% 100% no-repeat,
      radial-gradient(70% 50% at 30% 110%, #0b1225 0%, transparent 60%) bottom left / 60% 90% no-repeat,
      radial-gradient(70% 50% at 70% 110%, #0b1225 0%, transparent 60%) bottom right / 60% 90% no-repeat;
  z-index: 0;
}

/* ====== 헤드라인 & CTA ====== */
.headline-wrap {
  position: relative;
  z-index: 2;
  max-width: 960px;
  margin-inline: auto;      /* 중앙 정렬, 좌우 여백 없음 */
}
.headline {
  margin: 0 0 8px;
  line-height: 1.15;
  font-weight: 800;
  font-size: clamp(1.8rem, 3.6vw + 1rem, 3.6rem);
  letter-spacing: -0.02em;
  text-shadow: 0 2px 18px rgba(0,0,0,.45);
}
.sep { opacity: .8; }
.accent {
  background: linear-gradient(90deg, #ffd86b, #ffe59e 45%, #ffd86b 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  text-shadow: none;
}

.sub {
  margin: 8px 0 18px;
  opacity: .95;
  font-size: clamp(1rem, 1vw + .6rem, 1.25rem);
}

.cta-row {
  display: inline-flex;
  gap: 12px;
  flex-wrap: wrap;
  justify-content: center;
}

.btn {
  border: 1px solid rgba(255,255,255,.18);
  background: rgba(255,255,255,.04);
  color: #f6f7fb;
  padding: 10px 16px;
  border-radius: 999px;
  font-weight: 700;
  letter-spacing: .02em;
  cursor: pointer;
  transition: transform .2s ease, background .2s ease, border-color .2s ease;
  text-decoration: none;
  user-select: none;
  -webkit-tap-highlight-color: transparent;
}
.btn:hover { transform: translateY(-1px); background: rgba(255,255,255,.08); }
.btn:active { transform: translateY(0); }
.btn.primary {
  border-color: rgba(255, 214, 104, .6);
  background: linear-gradient(180deg, rgba(255, 214, 104, .35), rgba(255, 214, 104, .15));
  box-shadow: 0 4px 20px rgba(255, 214, 104, .15), inset 0 0 12px rgba(255, 214, 104, .18);
}
.btn.ghost { background: transparent; }

/* ====== 카드 ====== */
.card {
  max-width: 960px;
  margin: 0 auto;
  background: linear-gradient(180deg, rgba(255,255,255,.06), rgba(255,255,255,.03));
  border: 1px solid rgba(255,255,255,.12);
  border-radius: 20px;
  padding: clamp(16px, 3vw, 28px);
  backdrop-filter: blur(6px);
  box-shadow: 0 20px 60px rgba(0,0,0,.35);
}
.card-title {
  margin: 0 0 8px;
  font-size: clamp(1.2rem, 1.2vw + .9rem, 1.8rem);
}
.card-text {
  margin: 0 0 14px;
  opacity: .95;
  line-height: 1.7;
}
.chips {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
  padding: 0; margin: 0;
  list-style: none;
}
.chip {
  padding: 6px 10px;
  border-radius: 999px;
  background: rgba(255,255,255,.08);
  border: 1px solid rgba(255,255,255,.14);
  font-size: .9rem;
  opacity: .95;
}

/* ====== 연등 ====== */
.lanterns {
  position: absolute;
  inset: 0;
  pointer-events: none;
  z-index: 1;
}
.lantern {
  position: absolute;
  bottom: -8vh;
  width: clamp(20px, 1.8vw + 14px, 38px);
  height: clamp(26px, 2.2vw + 18px, 60px);
  transform: translateY(0);
  animation-name: floatUp;
  animation-iteration-count: infinite;
  animation-timing-function: linear;
}
.lantern-body {
  width: 100%; height: 78%;
  background: radial-gradient(80% 60% at 50% 30%, rgba(255,190,80,.95), rgba(240,140,50,.95) 60%, rgba(220,110,40,.95) 100%);
  border-radius: 10px 10px 8px 8px / 14px 14px 8px 8px;
  box-shadow:
      0 0 10px rgba(255,180,80,.55),
      0 6px 16px rgba(0,0,0,.35) inset;
}
.lantern-light {
  position: absolute;
  top: 38%;
  left: 50%;
  transform: translateX(-50%);
  width: 40%; height: 26%;
  background: radial-gradient(circle at 50% 50%, rgba(255,230,180,.9), rgba(255,210,120,.65) 60%, transparent 70%);
  filter: blur(1px);
  border-radius: 50%;
  opacity: .9;
}
.lantern-tail {
  position: absolute;
  bottom: -16%;
  left: 50%;
  width: 22%;
  height: 22%;
  transform: translateX(-50%);
  background: linear-gradient(180deg, rgba(240,140,50,1), rgba(180,70,20,1));
  clip-path: polygon(50% 0, 70% 40%, 50% 100%, 30% 40%);
  box-shadow: 0 2px 6px rgba(0,0,0,.35);
}

@keyframes floatUp {
  0%   { transform: translate(-50%, 0) scale(1); }
  95%  { transform: translate(-50%, -110svh) scale(1.02); opacity: 1; }
  100% { transform: translate(-50%, -110svh) scale(1.02); opacity: 0; }
}

/* ====== 접근성과 선호도 ====== */
@media (prefers-reduced-motion: reduce) {
  .stars, .cloud, .lantern, .btn { animation: none !important; transition: none !important; }
}

/* ====== 유틸 ====== */
::selection { background: rgba(255, 220, 120, .3); }
a { color: inherit; }
</style>