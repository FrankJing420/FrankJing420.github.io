---
layout: about
title: about
permalink: /
subtitle: >
  <a href="https://www.xjtu.edu.cn/" target="_blank">Xi'an Jiaotong University</a>
  &middot; 西安交通大学

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false
  more_info: >
    <p>
      <i class="fa-solid fa-envelope"></i>
      <a href="mailto:Frankjing420@163.com">Frankjing420@163.com</a>
    </p>
    <p>
      <i class="fa-solid fa-location-dot"></i>
      Xi'an, Shaanxi, China
    </p>

selected_papers: true
social: true

announcements:
  enabled: false

latest_posts:
  enabled: false
---

<div style="text-align:right; margin-bottom:1em;">
  <button id="lang-btn" onclick="toggleLang()"
    style="padding:3px 10px; border:1px solid #999; border-radius:4px; cursor:pointer; font-size:0.85em; background:transparent;">
    中文
  </button>
</div>

<div id="content-en">

I am an undergraduate student at [Xi'an Jiaotong University](https://www.xjtu.edu.cn/).
My research focuses on **low-count noise denoising in fluorescence microscopy imaging**,
with particular interest in two-photon, confocal, and MINFLUX imaging scenarios.

My research philosophy centers on combining **physics-based modeling with self-supervised learning**.
I leverage the physical states, constraints, and redundancy inherent in imaging systems to guide
algorithm design — alleviating the ill-posed recovery problem under single observations and
improving reconstruction quality in low-count regimes.

Building on this foundation, I further explore **physics-aware agents and adaptive schedulers**
that dynamically select temporal, spatial, or other redundancy sources based on current imaging
states, towards more robust and interpretable denoising strategies.

**Research Interests**

- Fluorescence microscopy denoising (two-photon, confocal, MINFLUX)
- Self-supervised learning with physics-based modeling
- Image reconstruction under low-count conditions
- Physics-aware adaptive scheduling algorithms

</div>

<div id="content-zh" style="display:none;">

我是西安交通大学的本科生。
我的研究聚焦于**荧光显微成像中的低计数噪声去噪**，
尤其关注双光子、共聚焦和 MINFLUX 等成像场景。

我的研究思路是将**物理建模与自监督学习**相结合——
利用成像系统内在的物理约束与数据冗余来指导算法设计，
缓解单次观测下的病态重建问题，提升低计数条件下的图像质量。

在此基础上，我进一步探索**物理状态感知的智能体与自适应调度算法**，
根据当前成像状态动态选择时间、空间或其他冗余信息，
实现更鲁棒、更可解释的去噪策略。

**研究兴趣**

- 显微成像去噪（双光子、共聚焦、MINFLUX）
- 自监督学习与物理建模结合
- 低计数条件下的图像重建
- 物理状态感知的自适应调度算法

</div>

<script>
function toggleLang() {
  var en = document.getElementById('content-en');
  var zh = document.getElementById('content-zh');
  var btn = document.getElementById('lang-btn');
  if (zh.style.display === 'none') {
    zh.style.display = '';
    en.style.display = 'none';
    btn.textContent = 'EN';
  } else {
    en.style.display = '';
    zh.style.display = 'none';
    btn.textContent = '中文';
  }
}
</script>
