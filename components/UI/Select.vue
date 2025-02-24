<template>
  <div
    class="select"
    :class="{
      active: isOpen,
    }"
    tabindex="0"
    @blur="isOpen = false"
  >
    <!-- Выбранный элемент -->
    <div class="selected" @click="toggleDropdown">
      <span class="placeholder" v-if="!model">{{ props.label }}</span>
      <span v-else>{{
        model?.label.includes('access_')
          ? $t.access[model?.label]
          : model?.label
      }}</span>
      <i class="fa-regular fa-times" v-if="model" @click="removeActiveSelect" />
      <i class="fa-regular fa-caret-down" />
    </div>
    <!-- Список -->
    <ul v-if="isOpen" class="dropdown">
      <!-- Элемент списка -->
      <li
        v-for="option in options"
        :key="option.value || option.value"
        @click="selectOption(option)"
        :class="{
          active: option === model,
        }"
      >
        {{ option.label || option.label }}
      </li>
    </ul>
  </div>
</template>

<!-- ----------------------------------------------------- -->
<!-- ----------------------------------------------------- -->

<script lang="ts" setup>
import type { TSelect } from '~/types/base';

/**
 * Пропсы ----------------
 */
const props = defineProps<{
  label: string;
  options: TSelect[];
  modelValue: TSelect | null;
}>();

// Значения
const model = computed({
  get() {
    return props.modelValue;
  },
  set(val) {
    emits('update:modelValue', val);
  },
});

/**
 * События ----------------
 */
const emits = defineEmits(['update:modelValue', 'handleChange']);

/**
 * Переменные ----------------
 */
const isOpen = ref(false);
const $t = await useTranslate('data');

/**
 * Методы ----------------
 */
// Удалить активный элемент
const removeActiveSelect = () => {
  model.value = null;
};
// Переключение между открытием и закрытием селекта
const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};
// Выбрать нужный элемент из списка
const selectOption = (option: TSelect) => {
  model.value = option;
  isOpen.value = false;
  emits('handleChange');
};
</script>

<!-- ----------------------------------------------------- -->
<!-- ----------------------------------------------------- -->

<style lang="scss" scoped>
.select {
  position: relative;
  user-select: none;
  max-width: 300px;
  &.active {
    .fa-caret-down {
      transform: rotate(180deg);
    }
  }
}

.selected {
  cursor: pointer;
  display: flex;
  align-items: center;
  color: $black;
  &:hover {
    .fa-times {
      opacity: 1;
    }
  }
  .placeholder {
    font-size: 14px;
    color: $gray;
    transition: 0.3s;
    &:hover {
      color: $blue;
    }
  }
  span {
    font-size: 14px;
  }
  .fa-caret-down {
    margin-left: 10px;
  }
  .fa-times {
    margin-left: 15px;
    opacity: 0;
  }
}

.dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  max-height: 250px;
  overflow: auto;
  background-color: $white;
  border-radius: 2px;
  z-index: 100;
  margin-top: 15px;
  box-shadow: 0 0 3rem 0 rgba(0, 0, 0, 0.08);
  // ScrollBar
  &::-webkit-scrollbar {
    width: 3px;
  }
}

.dropdown li {
  padding: 12px 17px;
  font-size: 14px;
  cursor: pointer;
  &:hover {
    background-color: $blue2;
  }
  &.active {
    color: $blue;
  }
}
</style>
