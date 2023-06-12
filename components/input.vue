<template>
  <div class="input-box-container">
    <label>{{ label }}</label>
    <input
      class="input"
      title="Enter Cubes Per Row"
      :class="{ disable: disabled }"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
      :type="type"
      :disabled="disabled ? 'disabled' : null"
    />
  </div>
</template>
<script setup>
const props = defineProps({
  modelValue: {
    type: [String, Number],
  },
  validationClass: {
    type: Boolean,
    default: false,
  },
  label: {
    type: String,
    default: "Row:",
  },
  type: {
    type: String,
    default: "number",
  },
  disabled: {
    type: Boolean,
    default: false,
  },
});
defineEmits(["update:modelValue"]);
</script>
<style>
.input-box-container {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  justify-content: center;
  gap: 2.5px;
  background-color: inherit;
  transition: 0.5s;
}
.input {
  width: 100%;
  height: 35px;
  font-size: 24px;
  text-align: center;
  border: solid 2px rgb(17, 167, 24);
  outline: none;
}
.input.disable {
  background-color: #898989;
  cursor: not-allowed;
}
.error {
  border: solid 2px darkred;
}
.button {
  width: 50%;
  height: 35px;
  padding: 2px 15px;
  cursor: pointer;
  font-size: 24px;
  user-select: none;
  text-decoration: none;
  background-color: antiquewhite;
  text-align: center;
  position: relative;
  align-self: center;
}
.button a {
  position: absolute;
  left: 0;
  height: 0;
  width: 100%;
  height: 100%;
}
</style>
