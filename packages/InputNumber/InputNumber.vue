<template>
  <a-input
    v-bind="$attrs"
    :value="data"
    @change="handleChange"
    @blur="handleBlur"
    @focus="handleFocus"
  />
</template>
<script>
import {
  defineComponent,
  ref,
  watch,
  getCurrentInstance,
} from "@vue/composition-api";
export default defineComponent({
  model: {
    prop: "modelValue",
    event: "update:modalValue",
  },
  props: {
    modelValue: [Number, String],
  },
  setup(props, { emit }) {
    const vm = getCurrentInstance()?.proxy;
    const data = ref(0);
    const setValue = (value) => {
      if (data.value === value) {
        return;
      }

      data.value = value;
      emit("change", data.value);
      emit("input", data.value);
      emit("update:modalValue", data.value);
    };

    watch(
      () => props.modelValue,
      (value) => {
        let newValue = value === undefined ? value : Number(value);
        if (isNaN(newValue)) {
          return;
        }
        data.value = newValue;
        emit("update:modalValue", newValue);
      },
      { immediate: true }
    );

    return {
      data,
      handleChange(event) {
        const value = event.target.value;
        const newValue = value === "" ? undefined : Number(value);
        
        //+-.+.-.等符号均会被视为数值类型的一种，只是不会导致更新值，也不会限制输入。
        //仅限于完全匹配情况下。
        if (/^([+-]?\.?(?!\.))$/.test(value) && value) {
          return;
        }

        if (!isNaN(newValue) || value === "") {
          setValue(newValue);
        } else {
          vm?.$forceUpdate();
        }
      },
      handleBlur(event) {
        emit("blur", event);
      },
      handleFocus(event) {
        emit("focus", event);
      },
    };
  },
});
</script>