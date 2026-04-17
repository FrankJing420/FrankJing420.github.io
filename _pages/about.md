---
layout: about
title: about
permalink: /
subtitle: >
  <span class="subtitle-en"><a href="https://www.xjtu.edu.cn/" target="_blank">Xi'an Jiaotong University</a></span><span class="subtitle-zh" style="display:none;"><a href="https://www.xjtu.edu.cn/" target="_blank">西安交通大学</a></span>

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

<div id="content-en" markdown="1">

I am currently an undergraduate student at the School of Mechanical Engineering, [Xi'an Jiaotong University](https://www.xjtu.edu.cn/).
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

<div id="content-zh" style="display:none;" markdown="1">

我目前是西安交通大学机械工程学院的本科生（越杰班）。
我的研究聚焦于**荧光显微成像中的低光子数噪声去噪**，
尤其关注双光子、共聚焦及 MINFLUX 等成像场景。

我的研究思路是将**物理建模与自监督学习**相结合，
利用成像系统内在的物理状态、约束与冗余来指导算法设计，
缓解单次观测下的不适定重建问题，提升低光子数条件下的图像质量。

在此基础上，我进一步探索**物理感知的自适应调度算法**，
根据当前成像状态动态选择时间、空间或其他冗余信息来源，
实现更鲁棒、更可解释的去噪策略。

**研究方向**

- 荧光显微镜去噪（双光子、共聚焦、MINFLUX）
- 自监督学习与物理建模
- 低光子数条件下的图像重建
- 物理感知自适应调度算法

</div>

<script>
(function() {
  var lang = localStorage.getItem('site_lang') || 'en';
  window._applyLang = function(lang) {
    var en = document.getElementById('content-en');
    var zh = document.getElementById('content-zh');
    document.querySelectorAll('.subtitle-en').forEach(function(el){ el.style.display = lang==='zh'?'none':''; });
    document.querySelectorAll('.subtitle-zh').forEach(function(el){ el.style.display = lang==='zh'?'':'none'; });
    if (en) en.style.display = lang==='zh' ? 'none' : '';
    if (zh) zh.style.display = lang==='zh' ? '' : 'none';
    document.querySelectorAll('.lang-toggle-btn').forEach(function(btn){ btn.textContent = lang==='zh'?'EN':'中文'; });
  };
  window._applyLang(lang);
})();
</script>
