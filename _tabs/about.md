---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<div class="d-flex flex-column align-items-center text-center mb-4">
  <!-- Avatar -->
  <img src="/assets/img/avatar.png" alt="Frogman" class="rounded-circle avatar-border" width="160" height="160">
  
  <!-- Name -->
  <h2 class="fw-bold mb-1 frogman-name">Frogman</h2>
  
  <!-- Motto -->
  <div class="motto text-center">
    <p class="fst-italic text-muted motto-line">TRANSIRE SUUM PECTUS MUNDOQUE POTIRI</p>
    <p class="text-muted chinese motto-line">天行健 君子以自强不息</p>
  </div>

  <!-- Contact -->
  <div class="contact-bar">
    <a href="mailto:frogmanr9@gmail.com" class="icon-link">
      <i class="fas fa-envelope"></i>
      <span class="email-text">frogmanr9@gmail.com</span>
    </a>
  </div>
</div>

<!-- Custom CSS -->
<style>
/* Avatar border and shadow */
.avatar-border {
  border: 2px solid #fff;
  padding: 2px;
  box-shadow: 0 0 5px rgba(0,0,0,0.2);
}

/* Name font */
.frogman-name {
  font-weight: 700;
  font-family: "Inter", "Helvetica Neue", Arial, sans-serif;
}

/* Motto spacing */
.motto {
  margin-bottom: 0.75rem;
  line-height: 1.6;
}

.motto-line {
  margin: 0;
  line-height: 1.6;
}

/* Chinese line: only set font family, keep color/size unchanged */
.motto .chinese {
  font-family: "LXGW WenKai", "PingFang SC", "Microsoft YaHei", sans-serif;
  font-style: normal; /* no italic for Chinese */
}

/* Contact bar spacing */
.contact-bar {
  margin-top: 0.75rem;
}

/* Icon link */
.icon-link {
  text-decoration: none;
  color: inherit;
  display: inline-flex;
  align-items: center;
  font-size: 1rem;
}

/* Icon spacing */
.icon-link i {
  font-size: 1.2rem;
}

.email-text {
  margin-left: 0.5rem;
}

@media (max-width: 480px) {
  .icon-link {
    flex-wrap: wrap;
    word-break: break-all;
  }

  .icon-link i {
    margin-bottom: 0.25rem;
    margin-left: 0
  }
}
</style>

---

### About Me

Born and raised in Nanjing, currently residing in Shanghai.  
A sports enthusiast.  
My current focus is computer architecture.

---

### About This Site

This site serves as a personal blog.  
It was launched on August 20, 2025, built with Jekyll using the Chirpy theme, and deployed on GitHub Pages.  
Here, I share articles related to my professional work and personal interests — partly as a way to entertain myself, and partly in the hope of contributing to the community.  
Everyone is welcome to reach out and exchange ideas.

---

<img src="https://readme-typing-svg.demolab.com/?lines=Website+Statistics;Welcome+to+Frogman's+Blog&center=true&width=1000&height=50&size=32&font=Righteous" alt="Typing text animation">



<iframe src="/assets/html/site_runtime.html" style="border:none; width:100%; height:40px;"></iframe>



Total posts published: {{ site.posts | size }}.



Page Views (Moe Counter):

<img src="https://count.getloli.com/@frogman?name=frogman&theme=morden-num&padding=7&offset=0&align=center&scale=1&pixelated=1&darkmode=auto" alt="Site runtime counter">



<!-- Font Awesome CSS for icons -->
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" 
      integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" 
      crossorigin="anonymous">

<!-- Busuanzi visitor counter script -->
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<div style="margin:2rem auto; max-width:600px; text-align:center; padding:1rem; border:1px solid #ddd; border-radius:12px; box-shadow:0 2px 6px rgba(0,0,0,0.08);">
  <h3 style="margin-bottom:0.5rem;">
    <i class="fas fa-chart-bar" style="margin-right:6px;"></i>
    Busuanzi Statistics
  </h3>
  <div>
    <div>
      This site has been visited 
      <span id="busuanzi_value_site_pv">
        <i class="fa fa-spinner fa-spin"></i>
      </span> times by 
      <span id="busuanzi_value_site_uv">
        <i class="fa fa-spinner fa-spin"></i>
      </span> unique visitors.
    </div>
    <div>
      This page has 
      <span id="busuanzi_value_page_pv">
        <i class="fa fa-spinner fa-spin"></i>
      </span> views.
    </div>
  </div>
</div>
