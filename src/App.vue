<template>
  <main class="main">
    <Container>
      <div class="content-warpper">
        <div class="content-view">
          <Header />

          <div class="playground">
            <div class="avatar-container">
              <div class="avatar-wrapper">
                <VueColorAvatar
                  ref="colorAvatarRef"
                  :option="avatarOption"
                  :size="280"
                  :style="{
                    transform: `rotateY(${flipped ? -180 : 0}deg)`,
                  }"
                />
              </div>
            </div>

            <ActionBar @action="handleAction" class="action-bar" />

            <div class="action-group">
              <button
                type="button"
                class="action-btn action-randomize btn"
                @click="handleGenerate"
              >
                {{ t('action.randomize') }}
              </button>

              <button
                type="button"
                class="action-btn action-download btn"
                :disabled="downloading"
                @click="handleDownload"
              >
                <span v-if="downloading" class="loading-spinner"></span>
                {{
                  downloading
                    ? `${t('action.downloading')}...`
                    : t('action.download')
                }}
              </button>

              <button
                type="button"
                class="action-btn action-share btn"
                @click="handleOpenShare"
              >
                {{ t('action.share') }}
              </button>

              <button
                type="button"
                class="action-btn action-multiple btn"
                @click="() => generateMultiple()"
              >
                {{ t('action.downloadMultiple') }}
              </button>
            </div>
          </div>

          <Footer />

          <CodeModal :visible="codeVisible" @close="codeVisible = false" />

          <DownloadModal
            :visible="downloadModalVisible"
            :image-url="imageDataURL"
            @close=";(downloadModalVisible = false), (imageDataURL = '')"
          />
        </div>

        <ConfettiCanvas />

        <div class="gradient-bg">
          <div class="gradient-top"></div>
          <div class="gradient-bottom"></div>
        </div>
      </div>
    </Container>

    <BatchDownloadModal
      :visible="avatarListVisible"
      :avatar-list="avatarList"
      @regenerate="generateMultiple"
      @close=";(avatarListVisible = false), (avatarList = [])"
    />

    <Sider>
      <Configurator />
    </Sider>

    <ShareModal
      :visible="shareModalVisible"
      :image-url="imageShareURL"
      @close="shareModalVisible = false"
      @wechat-share="qrcodeVisible = true"
    />

    <QRCodeModal
      :visible="qrcodeVisible"
      :url="shareUrl"
      @close="qrcodeVisible = false"
    />
  </main>
</template>

<script lang="ts" setup>
import { ref, watchEffect, computed } from 'vue'
import { useI18n } from 'vue-i18n'

import ActionBar from '@/components/ActionBar.vue'
import Configurator from '@/components/Configurator.vue'
import BatchDownloadModal from '@/components/Modal/BatchDownloadModal.vue'
import CodeModal from '@/components/Modal/CodeModal.vue'
import DownloadModal from '@/components/Modal/DownloadModal.vue'
import VueColorAvatar, {
  type VueColorAvatarRef,
} from '@/components/VueColorAvatar.vue'
import { ActionType } from '@/enums'
import { useAvatarOption } from '@/hooks'
import Container from '@/layouts/Container.vue'
import Footer from '@/layouts/Footer.vue'
import Header from '@/layouts/Header.vue'
import Sider from '@/layouts/Sider.vue'
import { useStore } from '@/store'
import { REDO, UNDO } from '@/store/mutation-type'
import {
  getRandomAvatarOption,
  getSpecialAvatarOption,
  showConfetti,
} from '@/utils'
import {
  DOWNLOAD_DELAY,
  NOT_COMPATIBLE_AGENTS,
  TRIGGER_PROBABILITY,
} from '@/utils/constant'
import { recordEvent } from '@/utils/ga'

import { name as appName } from '../package.json'
import ConfettiCanvas from './components/ConfettiCanvas.vue'
import type { AvatarOption } from './types'
import SocialShare from '@/components/SocialShare.vue'
import QRCodeModal from '@/components/Modal/QRCodeModal.vue'
import ShareModal from '@/components/Modal/ShareModal.vue'

const store = useStore()

const [avatarOption, setAvatarOption] = useAvatarOption()

const { t } = useI18n()

const colorAvatarRef = ref<VueColorAvatarRef>()

function handleGenerate() {
  if (Math.random() <= TRIGGER_PROBABILITY) {
    let colorfulOption = getSpecialAvatarOption()
    while (
      JSON.stringify(colorfulOption) === JSON.stringify(avatarOption.value)
    ) {
      colorfulOption = getSpecialAvatarOption()
    }
    colorfulOption.wrapperShape = avatarOption.value.wrapperShape
    setAvatarOption(colorfulOption)
    showConfetti()
  } else {
    const randomOption = getRandomAvatarOption(avatarOption.value)
    setAvatarOption(randomOption)
  }

  recordEvent('click_randomize', {
    event_category: 'click',
  })
}

const downloadModalVisible = ref(false)
const downloading = ref(false)
const imageDataURL = ref('')

async function handleDownload() {
  try {
    downloading.value = true
    const avatarEle = colorAvatarRef.value?.avatarRef

    const userAgent = window.navigator.userAgent.toLowerCase()
    const notCompatible = NOT_COMPATIBLE_AGENTS.some(
      (agent) => userAgent.indexOf(agent) !== -1
    )

    if (avatarEle) {
      const html2canvas = (await import('html2canvas')).default
      const canvas = await html2canvas(avatarEle, {
        backgroundColor: null,
      })
      const dataURL = canvas.toDataURL()

      if (notCompatible) {
        imageDataURL.value = dataURL
        downloadModalVisible.value = true
      } else {
        const trigger = document.createElement('a')
        trigger.href = dataURL
        trigger.download = `${appName}.png`
        trigger.click()
      }
    }

    recordEvent('click_download', {
      event_category: 'click',
    })
  } finally {
    setTimeout(() => {
      downloading.value = false
    }, DOWNLOAD_DELAY)
  }
}

const flipped = ref(false)
const codeVisible = ref(false)

function handleAction(actionType: ActionType) {
  switch (actionType) {
    case ActionType.Undo:
      store[UNDO]()
      recordEvent('action_undo', {
        event_category: 'action',
        event_label: 'Undo',
      })
      break

    case ActionType.Redo:
      store[REDO]()
      recordEvent('action_redo', {
        event_category: 'action',
        event_label: 'Redo',
      })
      break

    case ActionType.Flip:
      flipped.value = !flipped.value
      recordEvent('action_flip_avatar', {
        event_category: 'action',
        event_label: 'Flip Avatar',
      })
      break

    case ActionType.Code:
      codeVisible.value = !codeVisible.value
      recordEvent('action_view_code', {
        event_category: 'action',
        event_label: 'View Avatar Option Code',
      })
      break
  }
}

const avatarListVisible = ref(false)
const avatarList = ref<AvatarOption[]>([])

watchEffect(() => {
  avatarListVisible.value =
    Array.isArray(avatarList.value) && avatarList.value.length > 0
})

async function generateMultiple(count = 5 * 6) {
  const { default: hash } = await import('object-hash')

  const avatarMap = [...Array(count)].reduce<Map<string, AvatarOption>>(
    (res) => {
      let randomAvatarOption: AvatarOption
      let hashKey: string

      do {
        randomAvatarOption = getRandomAvatarOption(avatarOption.value)
        hashKey = hash.sha1(randomAvatarOption)
      } while (
        randomAvatarOption.background.color === 'transparent' ||
        res.has(hashKey)
      )

      res.set(hashKey, randomAvatarOption)

      return res
    },
    new Map()
  )

  avatarList.value = Array.from(avatarMap.values())

  recordEvent('click_generate_multiple', {
    event_category: 'click',
  })
}

const shareModalVisible = ref(false)
const qrcodeVisible = ref(false)
const imageShareURL = ref('')
const shareUrl = computed(() => typeof window !== 'undefined' ? window.location.href : '')

async function handleOpenShare() {
  try {
    const avatarEle = colorAvatarRef.value?.avatarRef
    
    if (avatarEle) {
      const html2canvas = (await import('html2canvas')).default
      const canvas = await html2canvas(avatarEle, {
        backgroundColor: null,
        scale: 2,
        logging: false,
        allowTaint: false,
        useCORS: true,
        onclone: (documentClone) => {
          const clonedElement = documentClone.querySelector('[data-html2canvas-clone-id]');
          if (clonedElement && clonedElement instanceof HTMLElement) {
            clonedElement.style.background = 'transparent';
          }
        }
      })
      
      imageShareURL.value = canvas.toDataURL('image/png', 1.0)
    }
    
    shareModalVisible.value = true

    recordEvent('click_share', {
      event_category: 'click'
    })
  } catch (error) {
    console.error('Failed to prepare image for sharing', error)
  }
}
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.main {
  width: 100%;
  height: 100%;
  overflow: hidden;
  color: var.$color-text;
  background-color: var.$color-page-bg;

  .content-warpper {
    height: 100%;
    transform: scale(1);

    .content-view {
      position: relative;
      z-index: 110;
      display: flex;
      flex-direction: column;
      height: 100%;
      overflow-y: auto;
    }
  }
}

.playground {
  display: flex;
  flex: 1;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 2rem 0;
  
  .avatar-container {
    background: radial-gradient(circle at center, rgba(255,255,255,0.05) 0%, transparent 70%);
    padding: 2rem;
    border-radius: 50%;
  }

  .avatar-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    transition: transform var.$transition-normal;
    filter: drop-shadow(0 10px 15px rgba(0, 0, 0, 0.2));
    
    &:hover {
      transform: translateY(-5px);
    }

    @media screen and (max-width: var.$screen-sm) {
      transform: scale(0.85);
    }
  }
  
  .action-bar {
    margin-top: 2rem;
  }

  .action-group {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 3rem;
    column-gap: 1rem;
    flex-wrap: wrap;

    @media screen and (max-width: var.$screen-sm) {
      column-gap: 0.7rem;
      row-gap: 0.7rem;
    }

    .action-btn {
      min-width: 6.5rem;
      height: 2.7rem;
      padding: 0 1.2rem;
      font-size: 0.95rem;
      font-weight: 500;
      letter-spacing: 0.01rem;
      color: var.$color-text;
      border: 1px solid rgba(255, 255, 255, 0.08);
      border-radius: 100px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
      backdrop-filter: blur(4px);
      transition: all 0.25s ease;
      
      &:hover {
        transform: translateY(-2px);
        box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
        border-color: rgba(255, 255, 255, 0.15);
      }
      
      &:active {
        transform: translateY(0);
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.15);
      }

      &:disabled,
      &[disabled] {
        opacity: 0.5;
        transform: none !important;
        cursor: not-allowed;
      }
      
      // 为各个按钮添加浅色渐变效果
      &.action-randomize {
        background: linear-gradient(135deg, rgba(242, 102, 255, 0.12) 0%, rgba(200, 80, 240, 0.08) 100%);
        
        &:hover {
          background: linear-gradient(135deg, rgba(242, 102, 255, 0.18) 0%, rgba(200, 80, 240, 0.14) 100%);
        }
      }
      
      &.action-download {
        background: linear-gradient(135deg, rgba(59, 230, 180, 0.12) 0%, rgba(50, 200, 160, 0.08) 100%);
        
        &:hover {
          background: linear-gradient(135deg, rgba(59, 230, 180, 0.18) 0%, rgba(50, 200, 160, 0.14) 100%);
        }
      }
      
      &.action-share {
        background: linear-gradient(135deg, rgba(100, 100, 240, 0.12) 0%, rgba(80, 80, 210, 0.08) 100%);
        
        &:hover {
          background: linear-gradient(135deg, rgba(100, 100, 240, 0.18) 0%, rgba(80, 80, 210, 0.14) 100%);
        }
      }
      
      &.action-multiple {
        background: linear-gradient(135deg, rgba(200, 200, 255, 0.12) 0%, rgba(160, 160, 220, 0.08) 100%);
        
        &:hover {
          background: linear-gradient(135deg, rgba(200, 200, 255, 0.18) 0%, rgba(160, 160, 220, 0.14) 100%);
        }
      }
      
      // 加载动画
      .loading-spinner {
        display: inline-block;
        width: 0.9rem;
        height: 0.9rem;
        margin-right: 0.5rem;
        border: 2px solid rgba(255,255,255,0.2);
        border-top-color: white;
        border-radius: 50%;
        animation: spin 0.8s linear infinite;
      }
      
      @keyframes spin {
        to { transform: rotate(360deg); }
      }
    }

    @media screen and (max-width: var.$screen-sm) {
      .action-multiple {
        display: none;
      }
    }
  }
}

@supports (filter: blur(4rem)) or (-webkit-filter: blur(4rem)) or
  (-moz-filter: blur(4rem)) {
  .gradient-bg {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;

    @mixin gradient-style($color) {
      position: absolute;
      width: 100vh;
      height: 100vh;
      background-image: radial-gradient(
        rgba($color, 0.8) 10%,
        rgba($color, 0.6) 30%,
        rgba($color, 0.4) 50%,
        rgba($color, 0.2) 70%,
        transparent 100%
      );
      border-radius: 50%;
      opacity: 0.15;
      filter: blur(5rem);
      animation: pulse 8s ease-in-out infinite alternate;
    }
    
    @keyframes pulse {
      0% {
        transform: scale(1);
        opacity: 0.15;
      }
      100% {
        transform: scale(1.1);
        opacity: 0.2;
      }
    }

    .gradient-top {
      @include gradient-style(var.$color-secondary);
      top: -40%;
      right: -20%;
    }

    .gradient-bottom {
      @include gradient-style(var.$color-accent);
      bottom: -40%;
      left: -20%;
      animation-delay: 2s;
    }
  }
}
</style>
