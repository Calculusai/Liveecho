<template>
  <header class="header glass">
    <div class="header-left">
      <Logo class="logo" />
      <h2 class="site-title">Live Echo</h2>
    </div>

    <div class="header-right">
      <a
        href="https://github.com/Calculusai/Liveecho"
        target="_blank"
        rel="nofollow noopener noreferrer"
        class="github-link"
      >
        <button
          type="button"
          class="github-button"
          @click="
            recordEvent('click_github', {
              event_category: 'click',
            })
          "
        >
          <img :src="IconGitHub" alt="GitHub" class="github-icon" />
          <span class="text">GitHub</span>
        </button>
      </a>
    </div>
  </header>
</template>

<script lang="ts" setup>
import IconGitHub from '@/assets/icons/icon-github.svg'
import Logo from '@/components/Logo.vue'
import { recordEvent } from '@/utils/ga'
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: var.$layout-header-height;
  padding: 0 2rem;
  margin: 1rem 2rem;
  border-radius: var.$border-radius-lg;
  
  .header-left {
    display: flex;
    align-items: center;
    
    .logo {
      transition: transform var.$transition-fast;
      
      &:hover {
        transform: scale(1.05);
      }
    }
    
    .site-title {
      margin-left: 1rem;
      font-weight: 700;
      font-size: 1.7rem;
      background: linear-gradient(90deg, var.$color-secondary 0%, var.$color-accent 100%);
      background-clip: text;
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      cursor: default;
      letter-spacing: 0.02em;

      @media screen and (max-width: var.$screen-sm) {
        display: none;
      }
    }
  }

  .header-right {
    .github-link {
      text-decoration: none;
    }
    
    .github-button {
      position: relative;
      display: flex;
      align-items: center;
      justify-content: center;
      width: 8.5rem;
      height: 2.4rem;
      padding: 0 1rem;
      color: var.$color-text;
      font-weight: 600;
      font-size: 1rem;
      background: rgba(255, 255, 255, 0.06);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: var.$border-radius-md;
      box-shadow: var.$shadow-sm;
      cursor: pointer;
      transition: all var.$transition-fast;
      user-select: none;

      &:hover {
        transform: translateY(-2px);
        background: rgba(255, 255, 255, 0.1);
        box-shadow: var.$shadow-md;
      }
      
      &:active {
        transform: translateY(0);
      }

      .github-icon {
        height: 1.2rem;
        width: 1.2rem;
        opacity: 0.9;
      }

      .text {
        margin-left: 0.5rem;
        letter-spacing: 0.02rem;
      }
    }
  }
}
</style>
