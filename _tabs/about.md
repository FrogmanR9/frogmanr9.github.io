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

Born in Nanjing and currently living in Shanghai.  
A sports enthusiast.  
My current focus is computer architecture.

---

### About This Site

This site serves as a personal blog.  
It was launched on August 20, 2025, built with Jekyll using the Chirpy theme, and deployed on GitHub Pages.  
Here, I share articles related to my professional work and personal interests — partly as a way to experiment and explore, and partly in the hope of contributing to the community.  
Everyone is welcome to reach out and exchange ideas.
