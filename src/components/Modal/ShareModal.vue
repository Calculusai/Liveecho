<template>
  <div v-if="visible" class="share-modal">
    <div class="modal-overlay" @click="emit('close')"></div>
    <div class="modal-container">
      <div class="modal-header">
        <h3>{{ t('share.title') }}</h3>
        <button class="close-btn" @click="emit('close')">×</button>
      </div>
      <div class="modal-content">
        <div class="avatar-preview">
          <img v-if="imageUrl" :src="imageUrl" alt="Avatar preview" />
        </div>
        <SocialShare :image-url="imageUrl" @wechat-share="handleWechatShare" />
        <div class="copy-link">
          <input 
            type="text" 
            readonly 
            ref="linkInputRef"
            :value="currentUrl" 
          />
          <button @click="copyLink">{{ t('share.copyLink') }}</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue'
import { useI18n } from 'vue-i18n'
import SocialShare from '@/components/SocialShare.vue'

const { t } = useI18n()

const props = defineProps<{
  visible: boolean
  imageUrl: string
}>()

const emit = defineEmits<{
  (e: 'close'): void
  (e: 'wechat-share'): void
}>()

const linkInputRef = ref<HTMLInputElement | null>(null)

// 获取当前URL，确保在浏览器环境下才访问window对象
const currentUrl = computed(() => {
  return typeof window !== 'undefined' ? window.location.href : '';
});

// 复制链接
function copyLink() {
  if (linkInputRef.value && typeof document !== 'undefined') {
    linkInputRef.value.select()
    document.execCommand('copy')
    window.alert(t('share.linkCopied'))
  }
}

// 处理微信分享
function handleWechatShare() {
  emit('wechat-share')
}
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.share-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  
  .modal-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    backdrop-filter: blur(4px);
    -webkit-backdrop-filter: blur(4px);
  }
  
  .modal-container {
    position: relative;
    width: 400px;
    max-width: 90%;
    background-color: var.$color-configurator;
    border-radius: var.$border-radius-lg;
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.3);
    overflow: hidden;
    animation: modal-appear 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    
    .modal-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 1rem 1.5rem;
      border-bottom: 1px solid rgba(255, 255, 255, 0.1);
      
      h3 {
        margin: 0;
        font-size: 1.2rem;
        font-weight: 600;
        color: var.$color-text;
      }
      
      .close-btn {
        background: none;
        border: none;
        font-size: 1.5rem;
        color: var.$color-text;
        cursor: pointer;
        line-height: 1;
        padding: 0.2rem;
        transition: color 0.2s;
        
        &:hover {
          color: var.$color-highlight;
        }
      }
    }
    
    .modal-content {
      padding: 1.5rem;
      
      .avatar-preview {
        width: 150px;
        height: 150px;
        margin: 0 auto 1.5rem;
        border-radius: var.$border-radius-md;
        overflow: hidden;
        background-color: rgba(255, 255, 255, 0.1);
        display: flex;
        align-items: center;
        justify-content: center;
        
        img {
          max-width: 100%;
          max-height: 100%;
          display: block;
        }
      }
      
      .copy-link {
        margin-top: 1.5rem;
        display: flex;
        border-radius: var.$border-radius-sm;
        overflow: hidden;
        
        input {
          flex: 1;
          border: none;
          padding: 0.8rem 1rem;
          background-color: rgba(255, 255, 255, 0.1);
          color: var.$color-text;
          font-size: 0.9rem;
          outline: none;
        }
        
        button {
          padding: 0.8rem 1rem;
          background-color: var.$color-accent;
          border: none;
          color: white;
          font-weight: 500;
          cursor: pointer;
          transition: background-color 0.2s;
          white-space: nowrap;
          
          &:hover {
            background-color: lighten(var.$color-accent, 5%);
          }
        }
      }
    }
  }
}

@keyframes modal-appear {
  from {
    opacity: 0;
    transform: scale(0.9);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
</style> 