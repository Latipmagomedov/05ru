<script setup>
const emit = defineEmits(['undo', 'redo', 'formatText', 'addImage', 'copy'])
</script>

<template>
  <div class="control">
    <button class="control__btn" @click="emit('undo')">
      <img src="@/assets/icons/back.svg" alt="back" />
    </button>
    <button class="control__btn" @click="emit('redo')">
      <img src="@/assets/icons/forward.svg" alt="forward" />
    </button>
    <button class="control__btn" @click="emit('formatText', 'title')">
      <img src="@/assets/icons/title.svg" alt="title" />
    </button>
    <button class="control__btn" @click="emit('formatText', 'paragraph')">
      <img src="@/assets/icons/paragraph.svg" alt="paragraph" />
    </button>
    <button class="control__btn" @mousedown.prevent="emit('addImage')">
      <img src="@/assets/icons/image.svg" alt="image" />
    </button>
    <button class="control__copy" @click="emit('copy')">Скопировать HTML</button>
  </div>
</template>

<style scoped lang="scss">
.control {
  display: flex;

  &__btn {
    width: 42px;
    height: 38px;
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: 0.3s;

    &:not(:first-child) {
      margin-left: 12px;
    }

    &:hover {
      background-color: var(--dark-3);
    }

    &:active {
      background-color: var(--dark-2);
    }
  }

  &__copy {
    margin-left: 19px;
    background-color: transparent;
    color: var(--blue);
    font-size: 15px;

    &:active {
      color: #fff;
    }
  }
}
</style>
