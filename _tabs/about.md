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
  <p class="fst-italic text-muted motto">
    TRANSIRE SUUM PECTUS MUNDOQUE POTIRI
  </p>

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
  line-height: 1.4;
  margin-bottom: 0.75rem;
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

Page Views (Moe Counter):

<img src="https://count.getloli.com/@frogman?name=frogman&theme=morden-num&padding=7&offset=0&align=center&scale=1&pixelated=1&darkmode=auto" alt="Site runtime counter">



<iframe src="/assets/html/site_runtime.html" style="border:none; width:100%; height:40px;"></iframe>



<!-- Font Awesome CSS for icons -->
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" 
      integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" 
      crossorigin="anonymous">

<!-- Busuanzi visitor counter script -->
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<span>
  This site has been visited 
  <span id="busuanzi_value_site_pv">
    <!-- Loading spinner before data is fetched -->
    <i class="fa fa-spinner fa-spin"></i>
  </span> times by 
  <span id="busuanzi_value_site_uv">
    <!-- Loading spinner before data is fetched -->
    <i class="fa fa-spinner fa-spin"></i>
  </span> unique visitors.  
  This page has 
  <span id="busuanzi_value_page_pv">
    <!-- Loading spinner before data is fetched -->
    <i class="fa fa-spinner fa-spin"></i>
  </span> views (Busuanzi).
</span>
