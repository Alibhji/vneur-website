<template>
  <a 
    :href="emailLink" 
    class="email-link"
    @click.prevent="handleEmailClick"
  >
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M4 4H20C21.1 4 22 4.9 22 6V18C22 19.1 21.1 20 20 20H4C2.9 20 2 19.1 2 18V6C2 4.9 2.9 4 4 4Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      <path d="L22 6L12 13L2 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
    Send Email
  </a>
</template>

<script>
export default {
  name: 'EmailLink',
  data() {
    return {
      email: 'contact@vnour.com' // Change this to your email
    }
  },
  computed: {
    emailLink() {
      return `mailto:${this.email}?subject=Contact from VNour Website`
    }
  },
  methods: {
    handleEmailClick() {
      window.location.href = this.emailLink
    }
  }
}
</script>

<style scoped>
.email-link {
  position: fixed;
  bottom: 30px;
  right: 30px;
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 24px;
  background: rgba(0, 255, 136, 0.1);
  border: 2px solid #00ff88;
  border-radius: 50px;
  color: #00ff88;
  text-decoration: none;
  font-size: 16px;
  font-weight: 600;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
  z-index: 1000;
}

.email-link:hover {
  background: rgba(0, 255, 136, 0.2);
  transform: translateY(-2px);
  box-shadow: 0 10px 30px rgba(0, 255, 136, 0.3);
}

.email-link svg {
  width: 20px;
  height: 20px;
}

@media (max-width: 768px) {
  .email-link {
    bottom: 20px;
    right: 20px;
    padding: 10px 20px;
    font-size: 14px;
  }
}
</style>

