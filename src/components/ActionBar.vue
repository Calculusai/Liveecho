<template>
  <div class="action-menu glass">
    <div
      v-for="ac in actions"
      :key="ac.type"
      class="menu-item"
      :class="{ disabled: ac.disabled }"
      :title="ac.tip"
      @click="emit('action', ac.type)"
    >
      <img :src="ac.icon" :alt="ac.tip" />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

import IconBack from '@/assets/icons/icon-back.svg'
import IconCode from '@/assets/icons/icon-code.svg'
import IconFlip from '@/assets/icons/icon-flip.svg'
import IconNext from '@/assets/icons/icon-next.svg'
import { ActionType } from '@/enums'
import { useStore } from '@/store'

const emit = defineEmits<{
  (e: 'action', actionType: ActionType): void
}>()

const { t } = useI18n()

const store = useStore()

const canUndo = computed(() => store.history.past.length > 0)
const canRedo = computed(() => store.history.future.length > 0)

const actions = computed(() => [
  {
    type: ActionType.Undo,
    icon: IconBack,
    tip: t('action.undo'),
    disabled: !canUndo.value,
  },
  {
    type: ActionType.Redo,
    icon: IconNext,
    tip: t('action.redo'),
    disabled: !canRedo.value,
  },
  {
    type: ActionType.Flip,
    icon: IconFlip,
    tip: t('action.flip'),
  },
  {
    type: ActionType.Code,
    icon: IconCode,
    tip: t('action.code'),
  },
])
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.action-menu {
  display: flex;
  align-items: center;
  padding: 0.5rem;
  background: rgba(25, 25, 35, 0.7);
  border: 1px solid rgba(255, 255, 255, 0.05);
  border-radius: var.$border-radius-lg;
  box-shadow: var.$shadow-md;

  .menu-item {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 2.5rem;
    height: 2.5rem;
    margin: 0 0.4rem;
    background: rgba(255, 255, 255, 0.04);
    border-radius: 50%;
    cursor: pointer;
    transition: all var.$transition-normal cubic-bezier(0.34, 1.56, 0.64, 1);
    overflow: hidden;
    
    &::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: linear-gradient(135deg, rgba(var.$color-accent, 0.15), rgba(var.$color-secondary, 0.15));
      opacity: 0;
      transition: opacity var.$transition-fast;
      z-index: 0;
    }
    
    &:hover:not(.disabled) {
      transform: translateY(-2px);
      box-shadow: var.$shadow-sm;
      
      &::before {
        opacity: 1;
      }
    }
    
    &:active:not(.disabled) {
      transform: translateY(0);
    }

    &.disabled {
      cursor: not-allowed;
      opacity: 0.35;
    }
    
    img {
      width: 1.2rem;
      height: 1.2rem;
      transition: transform var.$transition-fast ease;
      z-index: 1;
      position: relative;
      filter: drop-shadow(0 1px 2px rgba(0,0,0,0.1));
    }
    
    &:hover:not(.disabled) img {
      transform: scale(1.15);
    }
  }
}

@media (max-width: var.$screen-md) {
  .action-menu {
    padding: 0.4rem;
    
    .menu-item {
      width: 2.3rem;
      height: 2.3rem;
      margin: 0 0.3rem;
      
      img {
        width: 1.1rem;
        height: 1.1rem;
      }
    }
  }
}
</style>
