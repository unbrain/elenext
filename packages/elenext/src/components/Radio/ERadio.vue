<template>
  <label
    class="el-radio"
    :class="{
      'is-focus': isFocus,
      'is-checked': isChecked,
      'is-disabled': isDisabled,
    }"
  >
    <input
      ref="inputElRef"
      :checked="isChecked"
      :value="value"
      :name="name"
      :disabled="disabled"
      class="el-radio__input"
      type="radio"
      aria-hidden="true"
      :tabindex="-1"
      @focus="isFocus = true"
      @blur="isFocus = false"
      @change="changeHandler"
    />
    <span class="el-radio__label">
      <slot>{{ label }}</slot>
    </span>
  </label>
</template>
<script lang="ts">
import { App, computed, defineComponent, ref } from 'vue'
import { useParent } from '../../hooks/useParent'

import { VpTypes } from 'vptypes'
import { RADIOGROUP_IJK } from './core'

const ERadio = defineComponent({
  name: 'ERadio',
  props: {
    modelValue: VpTypes.oneOfType([VpTypes.string(), VpTypes.number()]),
    label: VpTypes.string(),
    value: VpTypes.oneOfType([VpTypes.string(), VpTypes.number()]).isRequired,
    disabled: VpTypes.bool(),
    name: VpTypes.string(),
    size: VpTypes.string(),
  },
  emits: ['change', 'update:modelValue'],
  setup(props, { attrs, slots, emit }) {
    const { parent } = useParent(RADIOGROUP_IJK)
    const inputElRef = ref<HTMLInputElement>()
    const isFocus = ref(false)
    const isDisabled = computed(() => {
      return parent?.disabled.value || props.disabled
    })

    const isChecked = computed(() => {
      if (parent) {
        return parent.modelValue.value === props.value
      }
      return props.modelValue === props.value
    })

    const changeHandler = (event: Event) => {
      emit('change', props.value)
      if (parent) {
        parent.change(props.value)
      } else {
        emit('update:modelValue', props.value)
      }
    }
    return {
      inputElRef,
      isChecked,
      isFocus,
      isDisabled,
      changeHandler,
    }
  },
})
ERadio.install = (app: App): void => {
  app.component(ERadio.name, ERadio)
}
export default ERadio
</script>
