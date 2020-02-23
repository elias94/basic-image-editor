<template>
  <div class="ImagePicker">
    <form class="ImagePicker__form">
      <Input :value="fileName" :prepend="inputText" :disabled="true" />
      <Button class="ImagePicker__uploadButton" v-on:click.native="onButtonClick">{{ buttonText }}</Button>
      <input ref="inputFile" type="file" style="display:none" v-on:change="onFileInputChange" />
    </form>
  </div>
</template>

<script>
import Input from '../atoms/Input.vue'
import Button from '../atoms/Button.vue'

export default {
  name: 'ImagePicker',
  components: {
    Input,
    Button,
  },
  props: {
    fileName: String,
    buttonText: String,
    inputText: String,
  },
  methods: {
    onButtonClick() {
      this.$refs.inputFile.click();
    },

    onFileInputChange(event) {
      this.$emit('file-upload', event.target.files[0]);
    },
  },
}
</script>

<style lang="scss">
.ImagePicker {
  padding: 20px 10px;

  &__form {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }
}
</style>
