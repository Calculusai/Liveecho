<template>
  <div class="social-share">
    <div v-if="showTitle" class="share-title">{{ t('action.share') }}</div>
    <div class="share-buttons">
      <button 
        v-for="platform in platforms" 
        :key="platform.id"
        class="share-button"
        :class="platform.id"
        :title="platform.name"
        @click="share(platform.id)"
      >
        <div class="icon">
          <Icon :icon="platform.iconName" />
        </div>
        <span v-if="showLabels" class="label">{{ platform.name }}</span>
      </button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue';
import { useI18n } from 'vue-i18n';
import { Icon } from '@iconify/vue';

const { t } = useI18n();

const props = withDefaults(defineProps<{
  url?: string;
  title?: string;
  description?: string;
  imageUrl?: string;
  showLabels?: boolean;
  showTitle?: boolean;
  appuid?: string;
}>(), {
  url: typeof window !== 'undefined' ? window.location.href : '',
  title: 'Vue Color Avatar',
  description: 'A customizable avatar generator',
  imageUrl: '',
  showLabels: false,
  showTitle: true,
  appuid: ''
});

const emit = defineEmits<{
  (e: 'wechat-share'): void;
}>();

// 可用的社交媒体平台
const platforms = computed(() => [
  {
    id: 'twitter',
    name: 'Twitter',
    iconName: 'simple-icons:twitter',
    shareUrl: () => `https://twitter.com/intent/tweet?url=${encodeURIComponent(props.url)}&text=${encodeURIComponent(props.title)}`
  },
  {
    id: 'facebook',
    name: 'Facebook',
    iconName: 'simple-icons:facebook',
    shareUrl: () => `https://www.facebook.com/sharer/sharer.php?u=${encodeURIComponent(props.url)}`
  },
  {
    id: 'wechat',
    name: t('share.wechat'),
    iconName: 'simple-icons:wechat',
    shareUrl: () => '#wechat-qrcode'
  }
]);

// 分享到社交媒体
const share = (platformId: string) => {
  if (typeof window === 'undefined') return;
  
  const platform = platforms.value.find(p => p.id === platformId);
  if (!platform) return;
  
  // 微信分享需要特殊处理（显示二维码）
  if (platformId === 'wechat') {
    emit('wechat-share');
    return;
  }
  
  // 记录分享事件
  try {
    // 检查window对象上是否有recordEvent方法
    const recordEvent = (window as any).recordEvent;
    if (typeof recordEvent === 'function') {
      recordEvent('share', {
        event_category: 'share',
        event_label: platformId,
      });
    }
  } catch (e) {
    console.error('Failed to record share event', e);
  }
  
  // 打开分享窗口
  const shareUrl = platform.shareUrl();
  window.open(shareUrl, '_blank', 'width=600,height=400');
};

// 在组件挂载时通知父组件
defineExpose({ share });
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.social-share {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.8rem;
  
  .share-title {
    font-weight: 600;
    font-size: 1rem;
    color: var.$color-text;
  }
  
  .share-buttons {
    display: flex;
    flex-wrap: wrap;
    gap: 0.8rem;
    justify-content: center;
    
    .share-button {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 0.5rem;
      padding: 0.8rem;
      border-radius: var.$border-radius-md;
      background-color: var.$color-configurator;
      color: var.$color-text;
      cursor: pointer;
      transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      border: none;
      
      &:hover {
        transform: translateY(-3px);
        box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
      }
      
      &:active {
        transform: translateY(-1px);
      }
      
      .icon {
        width: 24px;
        height: 24px;
        display: flex;
        align-items: center;
        justify-content: center;
        
        svg {
          width: 100%;
          height: 100%;
        }
      }
      
      &.twitter {
        background-color: rgba(29, 161, 242, 0.2);
        color: #1DA1F2;
        
        &:hover {
          background-color: rgba(29, 161, 242, 0.3);
        }
      }
      
      &.facebook {
        background-color: rgba(59, 89, 152, 0.2);
        color: #3B5998;
        
        &:hover {
          background-color: rgba(59, 89, 152, 0.3);
        }
      }
      
      &.xiaohongshu {
        background-color: rgba(255, 54, 54, 0.2);
        color: #FE2C55;
        
        &:hover {
          background-color: rgba(255, 54, 54, 0.3);
        }
      }
      
      &.wechat {
        background-color: rgba(7, 193, 96, 0.2);
        color: #07C160;
        
        &:hover {
          background-color: rgba(7, 193, 96, 0.3);
        }
      }
      
      &.qq {
        background-color: rgba(0, 120, 215, 0.2);
        color: #12B7F5;
        
        &:hover {
          background-color: rgba(0, 120, 215, 0.3);
        }
      }
    }
  }
}

@media (max-width: var.$screen-sm) {
  .social-share {
    .share-buttons {
      .share-button {
        padding: 0.6rem;
        
        .icon {
          width: 20px;
          height: 20px;
        }
      }
    }
  }
}
</style> 