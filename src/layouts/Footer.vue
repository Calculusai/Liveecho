<template>
  <footer class="footer glass">
    <div
      data-message="If you are deploying to your own public website, please do not modify it unless you have permission from the original author."
    >
      Made by
      <a
        class="link"
        href="https://leoku.top"
        target="_blank"
        rel="nofollow noopener noreferrer"
      >
        LeoKu
      </a>
      and
      <a
        class="link"
        href="https://hoshinoai.xin"
        target="_blank"
        rel="nofollow noopener noreferrer"
      >
        H.Ai
      </a>
    </div>

    <div class="divider">|</div>

    <div class="locale" @click="switchLocale">
      {{ locale === Locale.EN ? '简体中文' : 'English' }}
    </div>
  </footer>
</template>

<script lang="ts" setup>
import { useI18n } from 'vue-i18n'

import { Locale } from '@/enums'

const { locale } = useI18n()

function switchLocale() {
  locale.value = locale.value === Locale.EN ? Locale.ZH : Locale.EN
}
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.footer {
  display: flex;
  align-items: center;
  justify-content: center;
  height: var.$layout-footer-height;
  margin: 0 2rem 1rem;
  padding: 0 1.5rem;
  color: rgba(var.$color-text, 0.5);
  font-weight: 500;
  font-size: 0.85rem;
  border-radius: var.$border-radius-lg;
  letter-spacing: 0.01em;

  .divider {
    padding: 0 1rem;
    opacity: 0.7;
  }

  .locale {
    cursor: pointer;
    transition: all var.$transition-fast;
    padding: 0.3rem 0.7rem;
    border-radius: var.$border-radius-sm;
    
    &:hover {
      background: rgba(255, 255, 255, 0.08);
    }
  }

  .link,
  .locale {
    position: relative;
    transition: all var.$transition-fast;
    
    &:hover {
      color: var.$color-text;
    }
    
    &.link {
      &::after {
        content: '';
        position: absolute;
        left: 0;
        bottom: -2px;
        width: 0;
        height: 1px;
        background: linear-gradient(90deg, var.$color-secondary, var.$color-accent);
        transition: width var.$transition-normal;
      }
      
      &:hover::after {
        width: 100%;
      }
    }
  }
}
</style>
