<template>
  <input
    type="range"
    class="Slider"
    min="-100"
    max="100"
    :name="name"
    :value="value"
    v-on:input="onInput"
    v-on:mousedown="startDrag"
    v-on:mousemove="onDrag"
  />
</template>

<script>
export default {
  name: 'Slider',
  props: {
    value: String,
    name: String,
  },
  data() {
    return {
      isDragging: false,
    }
  },
  methods: {
    startDrag() {
      this.isDragging = true
    },
    stopDrag() {
      this.isDragging = false
    },
    onDrag(event) {
      if (this.isDragging) {
        this.$emit('mousemove', event)
      }
    },
    onInput(event) {
      this.$emit('input', event.target.value)
    },
  },
  mounted() {
    window.addEventListener('mouseup', this.stopDrag)
  },
  beforeDestroy() {
    window.removeEventListener('mouseup', this.stopDrag)
  },
}
</script>

<style lang="scss">
.Slider {
  -webkit-appearance: none;
  vertical-align: middle;
  outline: none;
  border: none;
  padding: 0;
  background: none;
  width: 300px;

  &::-webkit-slider-runnable-track {
    background-color: #d7dbdd;
    height: 6px;
    border-radius: 3px;
    border: 1px solid transparent;
  }

  &[disabled]::-webkit-slider-runnable-track {
    border: 1px solid #d7dbdd;
    background-color: transparent;
    opacity: 0.4;
  }

  &::-moz-range-track {
    background-color: #d7dbdd;
    height: 6px;
    border-radius: 3px;
    border: none;
  }

  &::-ms-track {
    color: transparent;
    border: none;
    background: none;
    height: 6px;
  }

  &::-ms-fill-lower { 
    background-color: #d7dbdd;
    border-radius: 3px;
  }

  &::-ms-fill-upper { 
    background-color: #d7dbdd;
    border-radius: 3px;
  }

  &::-ms-tooltip { display: none; /* display and visibility only */ }

  &::-moz-range-thumb {
    border-radius: 20px;
    height: 18px;
    width: 18px;
    border: none;
    background: none;
    background-color: #606670;
    border: 3px solid #FFF;
  }

  &:active::-moz-range-thumb {
    outline: none;
  }

  &::-webkit-slider-thumb {
    -webkit-appearance: none !important;
    border-radius: 100%;
    background-color: #606670;
    height: 18px;
    width: 18px;
    margin-top: -7px;
    border: 3px solid #FFF;
  }

  &[disabled]::-webkit-slider-thumb {
    background-color: transparent;
    border: 1px solid #d7dbdd;
  }

  &:active::-webkit-slider-thumb {
    outline: none;
  }

  &::-ms-thumb { 
    border-radius: 100%;
    background-color: #606670;
    height: 18px;
    width: 18px; 
    border: none;
    border: 3px solid #FFF;
  }

  &:active::-ms-thumb {
    border: none;
  }
}
</style>
