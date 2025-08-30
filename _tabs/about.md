---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

<!-- language switcher script -->
<script>
  function changeLanguage(lang) {
    /* Update the language display */
    if (lang === 'en') {
      document.getElementById('about-me-en').style.display = 'block';
      document.getElementById('about-me-zh').style.display = 'none';
      document.getElementById('about-this-site-en').style.display = 'block';
      document.getElementById('about-this-site-zh').style.display = 'none';
      document.querySelector('.slider').style.left = '0';  /* Slide to the left */
    } else if (lang === 'zh') {
      document.getElementById('about-me-en').style.display = 'none';
      document.getElementById('about-me-zh').style.display = 'block';
      document.getElementById('about-this-site-en').style.display = 'none';
      document.getElementById('about-this-site-zh').style.display = 'block';
      document.querySelector('.slider').style.left = '50%';  /* Slide to the right */
    }
  }
</script>



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


  /* Language Toggle Container */
  .language-toggle {
    position: relative;
    display: inline-block;
    width: 110px;
    height: 25px;
    margin-top: 1rem;
  }

  /* Language Bar */
  .language-bar {
    display: flex;
    position: relative;
    width: 100%;
    height: 100%;
    background-color: #f0f0f0; /* Softer background color */
    border-radius: 5px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.1); /* Light shadow */
  }

  /* Language Buttons */
  .btn-language {
    width: 50%;
    height: 100%;
    background: none;
    border: none;
    color: inherit;
    font-size: 12px;
    cursor: pointer;
    transition: background-color 0.3s;
    border-radius: 10px;
    z-index: 1;
  }

  .btn-language:hover {
    background-color: #dcd5d5ff; /* Soft hover effect */
  }

  .btn-language:focus {
    outline: none;
  }

  /* Slider (Small block that will slide) */
  .slider {
    position: absolute;
    top: 0;
    left: 0;
    width: 50%;
    height: 100%;
    background: transparent; /* Remove background color */
    border-radius: 10px;
    transition: left 0.3s ease; /* Smooth transition */
    z-index: 0;
    
    /* Use border + shadow for visual indication */
    box-shadow: 0 0 0 1.5px #a1b4ca; /* Thin border effect */
    opacity: 0.8; /* Slight transparency */
  }
</style>



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

  <!-- Language Switcher -->
  <div class="language-toggle" style="position:relative; width:110px; height:25px; margin-top: 1rem;">
    <div class="language-bar">
      <button onclick="changeLanguage('en')" class="btn-language">English</button>
      <button onclick="changeLanguage('zh')" class="btn-language">中文</button>
      <div class="slider"></div>
    </div>
  </div>
</div>

---

### About Me

<!-- Badge Rows -->
<div style="text-align:center; margin-bottom:1rem;">
  <!-- first row -->
  <div>
    <img src="https://img.shields.io/badge/Just-for_fun-blue?style=flat" 
        alt="Just for fun badge"
        style="display:inline-block; margin-right:6px; vertical-align:middle;">
    <img src="https://img.shields.io/badge/NASA-E03C31?style=flat&logo=nasa&logoColor=white" 
        alt="NASA badge"
        style="display:inline-block; margin-right:6px; vertical-align:middle;">
    <img src="https://img.shields.io/badge/FACTA_NON_VERBA-8A2BE2?style=flat" 
        alt="FACTA NON VERBA badge"
        style="display:inline-block; margin-right:6px; vertical-align:middle;">
  </div>
  <!-- second row -->
  <div>
    <img src="https://img.shields.io/badge/C-A8B9CC?style=flat&logo=c&logoColor=white" 
        alt="C badge"
        style="display:inline-block; margin-right:6px; vertical-align:middle;">
    <img src="https://img.shields.io/badge/C++-00599C?style=flat&logo=cplusplus&logoColor=white" 
        alt="C++ badge"
        style="display:inline-block; margin-right:6px; vertical-align:middle;">
    <img src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white" 
        alt="Python badge"
        style="display:inline-block; margin-right:6px; vertical-align:middle;">
  </div>
  <!-- third row -->
  <div>
    <img src="https://img.shields.io/badge/巨浪_巨浪-不断地增涨-1E90FF?style=flat" 
        alt="巨浪 巨浪 不断地增涨 badge"
        style="display:inline-block; margin-right:6px; vertical-align:middle;">
  </div>
</div>



<!-- About Me Section -->
<p id="about-me-en" class="about-text">
  Born and raised in Nanjing, currently residing in Shanghai.<br>
  A sports enthusiast.<br>
  My current focus is computer architecture.
</p>

<p id="about-me-zh" class="about-text" style="display:none;">
  出生、成长于南京，现居上海。<br>
  运动爱好者。<br>
  目前专注于计算机体系结构。
</p>

---

### About This Site

<!-- About This Site Section -->
<p id="about-this-site-en" class="about-text">
  This site serves as a personal blog.<br>
  It was launched on August 20, 2025, built with Jekyll using the Chirpy theme, and deployed on GitHub Pages.<br>
  Here, I share articles related to my professional work and personal interests — partly as a way to entertain myself, and partly in the hope of contributing to the community.<br>
  Everyone is welcome to reach out and exchange ideas.
</p>

<p id="about-this-site-zh" class="about-text" style="display:none;">
  本站建于 2025 年 8 月 20 日，基于 Jekyll 构建，采用 Chirpy 主题，并部署在 Github Pages 上。<br>
  本站用作个人博客，写一些专业和兴趣相关的文章，一来聊以自娱，二来也希望为社区做一点微小的贡献。<br>
  欢迎大家通过评论或邮件与我交流。
</p>



---

<img src="https://readme-typing-svg.demolab.com/?lines=Website+Statistics;Welcome+to+Frogman's+Blog&center=true&width=1000&height=50&size=32&font=Righteous" alt="Typing text animation">



<!-- Website Runtime Display -->
<div id="site-runtime-container" style="width:100%; 
        display:block;
        margin-bottom:1rem;
        background:inherit; 
        color:inherit;">
  <span id="site_runtime"></span>
</div>

<!-- running time calculation -->
<script>
  /* 
   * Set the website creation date in UTC format
   * This date should be when the site was first deployed
   */
  const siteCreateDate = new Date("2025-08-20T11:12:03Z");
  
  /*
   * Calculates and updates the website running time
   * This calculation is based purely on time differences (milliseconds)
   * It does not depend on calendar months/years, so it automatically
   * handles leap years, varying month lengths, daylight saving time changes, etc.
   */
  function updateRuntime() {
    const now = new Date();
    /* 
     * Calculate the difference in milliseconds
     * This is the most accurate method as it avoids calendar complexities
     */
    const diffMs = now - siteCreateDate;
    
    /* Calculate seconds (1 second = 1000 ms) */
    const totalSeconds = Math.floor(diffMs / 1000);
    const seconds = totalSeconds % 60;
    
    /* Calculate minutes (1 minute = 60 seconds) */
    const totalMinutes = Math.floor(totalSeconds / 60);
    const minutes = totalMinutes % 60;
    
    /* Calculate hours (1 hour = 60 minutes) */
    const totalHours = Math.floor(totalMinutes / 60);
    const hours = totalHours % 24;
    
    /* Calculate days (1 day = 24 hours) */
    const totalDays = Math.floor(totalHours / 24);
    
    /* Update the display */
    document.getElementById("site_runtime").textContent = 
      `This site has been running for ${totalDays} days ${hours} hours ${minutes} minutes ${seconds} seconds.`;
  }
  
  /* 
   * Initialize the timer:
   * 1. Update immediately when the page loads
   * 2. Set up an interval to update every second
   */
  updateRuntime();
  setInterval(updateRuntime, 1000);
</script>



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
