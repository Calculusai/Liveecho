<template>
  <div v-if="props.visible" class="qrcode-modal">
    <div class="modal-overlay" @click="emit('close')"></div>
    <div class="modal-container">
      <div class="modal-header">
        <h3>{{ t('share.qrcodeTitle') }}</h3>
        <button class="close-btn" @click="emit('close')">×</button>
      </div>
      <div class="modal-content">
        <div class="qrcode-container" ref="qrcodeContainerRef"></div>
        <p class="description">{{ t('share.scanToShare') }}</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted, watch } from 'vue';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();

const props = defineProps<{
  visible: boolean;
  url: string;
}>();

const emit = defineEmits<{
  (e: 'close'): void;
}>();

const qrcodeContainerRef = ref<HTMLDivElement | null>(null);

// 渲染二维码
const renderQRCode = async () => {
  if (!qrcodeContainerRef.value || !props.url) return;
  
  try {
    // 动态导入QRCode库
    const QRCode = (await import('qrcode')).default;
    
    // 清除容器内容
    qrcodeContainerRef.value.innerHTML = '';
    
    // 创建Canvas元素
    const canvas = document.createElement('canvas');
    qrcodeContainerRef.value.appendChild(canvas);
    
    // 生成二维码
    await QRCode.toCanvas(canvas, props.url, {
      width: 200,
      margin: 2,
      color: {
        dark: '#000000',
        light: '#ffffff'
      }
    });
  } catch (error) {
    console.error('Failed to generate QR code:', error);
    if (qrcodeContainerRef.value) {
      qrcodeContainerRef.value.innerHTML = `<p class="error">${t('share.qrcodeError')}</p>`;
    }
  }
};

watch(() => props.visible, (visible) => {
  if (visible) {
    // 当弹窗显示时生成二维码
    setTimeout(renderQRCode, 100);
  }
});

onMounted(() => {
  if (props.visible) {
    renderQRCode();
  }
});
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.qrcode-modal {
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
    width: 320px;
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
      display: flex;
      flex-direction: column;
      align-items: center;
      
      .qrcode-container {
        width: 200px;
        height: 200px;
        background-color: white;
        border-radius: var.$border-radius-md;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-bottom: 1rem;
        
        .error {
          color: var.$color-highlight;
          text-align: center;
          padding: 1rem;
        }
        
        canvas {
          display: block;
          max-width: 100%;
          max-height: 100%;
        }
      }
      
      .description {
        text-align: center;
        color: var.$color-text;
        font-size: 0.9rem;
        margin: 0;
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