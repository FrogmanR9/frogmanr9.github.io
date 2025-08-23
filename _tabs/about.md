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

<!-- Place this where you want the running time to appear -->
<span id="site_runtime"></span>

<script>
  // GitHub repository creation time
  const siteCreateDate = new Date("2025-08-20T11:12:03Z");

  function updateRuntime() {
    const now = new Date();

    // Calculate differences
    let years = now.getFullYear() - siteCreateDate.getFullYear();
    let months = now.getMonth() - siteCreateDate.getMonth();
    let days = now.getDate() - siteCreateDate.getDate();
    let hours = now.getHours() - siteCreateDate.getHours();
    let minutes = now.getMinutes() - siteCreateDate.getMinutes();
    let seconds = now.getSeconds() - siteCreateDate.getSeconds();

    // Handle borrow if negative
    if (seconds < 0) { seconds += 60; minutes--; }
    if (minutes < 0) { minutes += 60; hours--; }
    if (hours < 0) { hours += 24; days--; }
    if (days < 0) {
      // Get the number of days in the previous month
      const prevMonth = new Date(now.getFullYear(), now.getMonth(), 0).getDate();
      days += prevMonth;
      months--;
    }
    if (months < 0) { months += 12; years--; }

    // Convert years and months to days
    let totalDays = days;
    // Add days for the months
    for (let i = 0; i < months; i++) {
      const month = (siteCreateDate.getMonth() + i) % 12;
      const year = siteCreateDate.getFullYear() + Math.floor((siteCreateDate.getMonth() + i) / 12);
      totalDays += new Date(year, month + 1, 0).getDate();
    }
    // Add days for the years (including leap years)
    for (let y = 0; y < years; y++) {
      const year = siteCreateDate.getFullYear() + y;
      totalDays += (new Date(year, 1, 29).getMonth() === 1) ? 366 : 365;
    }

    // Display the running time
    document.getElementById("site_runtime").innerText =
      `This site has been running for ${totalDays} days ${hours} hours ${minutes} minutes ${seconds} seconds.`;
  }

  // Run once immediately
  updateRuntime();
  // Update every second
  setInterval(updateRuntime, 1000);
</script>



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
  </span> views.
</span>
