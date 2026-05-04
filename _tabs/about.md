---
layout: page
icon: fas fa-microchip
order: 4
---

<style>
/* 仅作用于本页包裹层，降低对全站主题的干扰 */
.about-lab {
  --al-cyan: #22d3ee;
  --al-violet: #a78bfa;
  --al-green: #34d399;
  --al-bg0: #030712;
  --al-bg1: #0f172a;
  --al-glass: rgba(15, 23, 42, 0.55);
  --al-border: rgba(34, 211, 238, 0.22);
  --al-text: #e2e8f0;
  --al-muted: #94a3b8;
  font-family: ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "PingFang SC", "Microsoft YaHei", sans-serif;
  color: var(--al-text);
  margin: -1.25rem -0.5rem 2rem;
  border-radius: 1rem;
  overflow: hidden;
  position: relative;
  isolation: isolate;
  box-shadow: 0 0 0 1px rgba(34, 211, 238, 0.08), 0 24px 80px -20px rgba(0, 0, 0, 0.65);
}

.about-lab::before {
  content: "";
  position: absolute;
  inset: 0;
  background:
    radial-gradient(1200px 500px at 10% -10%, rgba(34, 211, 238, 0.18), transparent 55%),
    radial-gradient(900px 400px at 95% 0%, rgba(167, 139, 250, 0.16), transparent 50%),
    linear-gradient(180deg, var(--al-bg0) 0%, var(--al-bg1) 45%, #020617 100%);
  z-index: -2;
}

.about-lab::after {
  content: "";
  position: absolute;
  inset: 0;
  background-image:
    linear-gradient(rgba(34, 211, 238, 0.06) 1px, transparent 1px),
    linear-gradient(90deg, rgba(34, 211, 238, 0.06) 1px, transparent 1px);
  background-size: 48px 48px;
  mask-image: radial-gradient(ellipse 90% 70% at 50% 0%, black 20%, transparent 75%);
  pointer-events: none;
  z-index: -1;
}

.about-lab__hero {
  padding: clamp(1.75rem, 4vw, 3rem) clamp(1.25rem, 3vw, 2.5rem);
  position: relative;
}

.about-lab__eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  font-size: 0.72rem;
  letter-spacing: 0.28em;
  text-transform: uppercase;
  color: var(--al-cyan);
  font-weight: 600;
  margin-bottom: 1rem;
  opacity: 0.95;
}

.about-lab__eyebrow span {
  display: inline-block;
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--al-green);
  box-shadow: 0 0 12px var(--al-green);
  animation: al-pulse 2s ease-in-out infinite;
}

@keyframes al-pulse {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(0.85); }
}

.about-lab__title {
  font-size: clamp(1.65rem, 4vw, 2.35rem);
  font-weight: 800;
  line-height: 1.15;
  margin: 0 0 0.75rem;
  letter-spacing: -0.02em;
  background: linear-gradient(105deg, #f8fafc 0%, var(--al-cyan) 40%, var(--al-violet) 85%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.about-lab__subtitle {
  font-size: 1.05rem;
  color: var(--al-muted);
  max-width: 40rem;
  line-height: 1.65;
  margin: 0 0 1.5rem;
}

.about-lab__actions {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
  align-items: center;
}

.about-lab__btn {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.65rem 1.15rem;
  border-radius: 0.65rem;
  font-weight: 600;
  font-size: 0.9rem;
  text-decoration: none !important;
  border: 1px solid transparent;
  transition: transform 0.15s ease, box-shadow 0.2s ease, border-color 0.2s ease;
}

.about-lab__btn--primary {
  color: #020617 !important;
  background: linear-gradient(135deg, var(--al-cyan), #2dd4bf);
  box-shadow: 0 0 0 1px rgba(34, 211, 238, 0.35), 0 12px 40px -12px rgba(34, 211, 238, 0.55);
}

.about-lab__btn--primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 0 0 1px rgba(34, 211, 238, 0.5), 0 16px 48px -10px rgba(34, 211, 238, 0.6);
}

.about-lab__btn--ghost {
  color: var(--al-text) !important;
  background: var(--al-glass);
  border-color: var(--al-border);
  backdrop-filter: blur(10px);
}

.about-lab__btn--ghost:hover {
  border-color: rgba(167, 139, 250, 0.45);
  box-shadow: 0 0 24px -8px rgba(167, 139, 250, 0.25);
}

.about-lab__metrics {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1px;
  margin: 0;
  padding: 0;
  list-style: none;
  background: rgba(148, 163, 184, 0.12);
  border-top: 1px solid rgba(148, 163, 184, 0.12);
}

.about-lab__metric {
  padding: 1rem 1.1rem;
  background: rgba(2, 6, 23, 0.45);
  backdrop-filter: blur(8px);
}

.about-lab__metric strong {
  display: block;
  font-size: 1.35rem;
  font-variant-numeric: tabular-nums;
  background: linear-gradient(90deg, var(--al-cyan), var(--al-violet));
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  margin-bottom: 0.2rem;
}

.about-lab__metric span {
  font-size: 0.78rem;
  color: var(--al-muted);
  letter-spacing: 0.04em;
}

.about-lab__body {
  padding: clamp(1.5rem, 3vw, 2.25rem) clamp(1.25rem, 3vw, 2.5rem) clamp(2rem, 4vw, 2.75rem);
  background: linear-gradient(180deg, rgba(2, 6, 23, 0.35) 0%, rgba(15, 23, 42, 0.25) 100%);
}

.about-lab__grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 1rem;
}

.about-lab__card {
  position: relative;
  padding: 1.25rem 1.35rem;
  border-radius: 0.85rem;
  background: rgba(15, 23, 42, 0.5);
  border: 1px solid rgba(148, 163, 184, 0.14);
  backdrop-filter: blur(12px);
  overflow: hidden;
  transition: border-color 0.2s ease, transform 0.2s ease;
}

.about-lab__card:hover {
  border-color: rgba(34, 211, 238, 0.35);
  transform: translateY(-2px);
}

.about-lab__card::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 2px;
  background: linear-gradient(90deg, transparent, var(--al-cyan), transparent);
  opacity: 0.65;
}

.about-lab__card h3 {
  margin: 0 0 0.5rem;
  font-size: 1rem;
  font-weight: 700;
  color: #f1f5f9;
  display: flex;
  align-items: center;
  gap: 0.45rem;
}

.about-lab__card h3 i {
  color: var(--al-cyan);
  opacity: 0.9;
}

.about-lab__card p {
  margin: 0;
  font-size: 0.88rem;
  line-height: 1.6;
  color: var(--al-muted);
}

.about-lab__tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.45rem;
  margin-top: 1.35rem;
}

.about-lab__tag {
  font-size: 0.72rem;
  padding: 0.28rem 0.6rem;
  border-radius: 999px;
  border: 1px solid rgba(167, 139, 250, 0.28);
  color: #ddd6fe;
  background: rgba(88, 28, 135, 0.15);
  letter-spacing: 0.02em;
}

.about-lab__footer {
  margin-top: 1.75rem;
  padding-top: 1.25rem;
  border-top: 1px dashed rgba(148, 163, 184, 0.2);
  font-size: 0.82rem;
  color: var(--al-muted);
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem 1.25rem;
  align-items: center;
}

.about-lab__footer code {
  font-size: 0.78rem;
  padding: 0.15rem 0.45rem;
  border-radius: 0.35rem;
  background: rgba(2, 6, 23, 0.6);
  border: 1px solid rgba(34, 211, 238, 0.2);
  color: var(--al-cyan);
}

.about-lab__footer a {
  color: var(--al-cyan) !important;
  text-decoration: none !important;
  font-weight: 600;
}

.about-lab__footer a:hover {
  text-decoration: underline !important;
}

@media (max-width: 600px) {
  .about-lab__metrics {
    grid-template-columns: 1fr;
  }
}
</style>

<div class="about-lab" markdown="0">
  <header class="about-lab__hero">
    <p class="about-lab__eyebrow"><span aria-hidden="true"></span> Digital Lab · Profile</p>
    <h2 class="about-lab__title">liu-XiaoShu · 小树</h2>
    <p class="about-lab__subtitle">
      个人技术笔记站：聚焦<strong style="color:#e2e8f0">嵌入式</strong>、<strong style="color:#e2e8f0">工具链与脚本</strong>，辅以 <strong style="color:#e2e8f0">C / Python</strong> 与工程踩坑记录。偏实战、可检索，写给未来的自己与同路人。
    </p>
    <div class="about-lab__actions">
      <a class="about-lab__btn about-lab__btn--primary" href="https://github.com/liu-XiaoShu" rel="noopener noreferrer" target="_blank">
        <i class="fab fa-github" aria-hidden="true"></i> GitHub
      </a>
      <a class="about-lab__btn about-lab__btn--ghost" href="mailto:liu_xiaoshu@163.com">
        <i class="fas fa-envelope" aria-hidden="true"></i> 邮件联系
      </a>
    </div>
  </header>

  <ul class="about-lab__metrics" aria-label="站点概览">
    <li class="about-lab__metric">
      <strong>笔记</strong>
      <span>技术 · 学习 · 生活</span>
    </li>
    <li class="about-lab__metric">
      <strong>栈</strong>
      <span>MCU · Linux 工具 · 自动化</span>
    </li>
    <li class="about-lab__metric">
      <strong>托管</strong>
      <span>Jekyll + GitHub Pages</span>
    </li>
  </ul>

  <div class="about-lab__body">
    <div class="about-lab__grid">
      <article class="about-lab__card">
        <h3><i class="fas fa-microchip" aria-hidden="true"></i> 嵌入式与硬件</h3>
        <p>驱动、外设与板级调试；把能在真机上跑通的步骤写清楚，减少「只在我电脑上能过」的幻觉。</p>
      </article>
      <article class="about-lab__card">
        <h3><i class="fas fa-terminal" aria-hidden="true"></i> 工具链与脚本</h3>
        <p>构建、刷写、批处理与小工具；能复制粘贴的命令会标注环境与版本，方便日后复盘。</p>
      </article>
      <article class="about-lab__card">
        <h3><i class="fas fa-code" aria-hidden="true"></i> 语言与工程</h3>
        <p>C / Python 为主，关注可读性与边界条件；文章尽量附上下文，而不是零散代码片段。</p>
      </article>
    </div>

    <div class="about-lab__tags" aria-label="技术标签">
      <span class="about-lab__tag">ESP32</span>
      <span class="about-lab__tag">嵌入式 Linux</span>
      <span class="about-lab__tag">CMake / Makefile</span>
      <span class="about-lab__tag">Python 自动化</span>
      <span class="about-lab__tag">Git · CI</span>
      <span class="about-lab__tag">Markdown 工作流</span>
    </div>

    <footer class="about-lab__footer">
      <span>本站由 <a href="https://jekyllrb.com/" rel="noopener noreferrer" target="_blank">Jekyll</a> 与 <a href="https://github.com/cotes2020/jekyll-theme-chirpy" rel="noopener noreferrer" target="_blank">Chirpy</a> 主题构建。</span>
      <span>发现笔误或希望交流：<code>liu_xiaoshu@163.com</code></span>
    </footer>
  </div>
</div>
