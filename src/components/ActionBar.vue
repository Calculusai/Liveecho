<template>
  <div class="action-menu">
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
  margin-top: 4rem;
  padding: 0.6rem;
  background-color: var.$color-gray;
  border-radius: var.$border-radius-lg;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);

  .menu-item {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 3rem;
    height: 3rem;
    margin: 0 0.5rem;
    background-color: var.$color-configurator;
    border-radius: 50%;
    cursor: pointer;
    transition: all 0.3s cubic-bezier(0.1, 0.7, 0.5, 1);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    
    &:hover:not(.disabled) {
      transform: translateY(-3px);
      background-color: var.$color-accent;
    }
    
    &:active:not(.disabled) {
      transform: translateY(0);
    }

    &.disabled {
      cursor: default;
      opacity: 0.4;
    }
    
    img {
      width: 1.5rem;
      height: 1.5rem;
      transition: transform 0.3s ease;
    }
    
    &:hover:not(.disabled) img {
      transform: scale(1.1);
    }
  }
}

@media (max-width: var.$screen-md) {
  .action-menu {
    margin-top: 3rem;
    padding: 0.5rem;
    
    .menu-item {
      width: 2.8rem;
      height: 2.8rem;
      margin: 0 0.4rem;
      
      img {
        width: 1.4rem;
        height: 1.4rem;
      }
    }
  }
}
</style>
