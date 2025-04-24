<template>
  <div class="setting-section">
    <div class="section-title">{{ props.title }}</div>
    <div class="section-content">
      <slot />
    </div>
  </div>
</template>

<script lang="ts" setup>
const props = defineProps<{
  title?: string
}>()
</script>

<style lang="scss" scoped>
@use 'src/styles/var';

.setting-section {
  padding: 1.5rem 1.2rem;
  margin-bottom: 0.5rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  
  &:last-child {
    border-bottom: none;
  }

  .section-title {
    margin-bottom: 1.2rem;
    font-weight: 600;
    line-height: 1.4;
    font-size: 1.1rem;
    color: var.$color-highlight;
    display: flex;
    align-items: center;
    
    &::before {
      content: "";
      display: inline-block;
      width: 4px;
      height: 18px;
      background: var.$color-highlight;
      margin-right: 8px;
      border-radius: 2px;
    }
  }
  
  .section-content {
    animation: fadeIn 0.4s ease-out;
  }
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(5px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>
