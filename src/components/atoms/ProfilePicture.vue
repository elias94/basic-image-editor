<template>
  <div class="ProfilePicture">
    <img class="ProfilePicture__image" :src="img" />
  </div>
</template>

<script>
export default {
  name: 'ProfilePicture',
  props: {
    img: String,
  },
}
</script>

<style lang="scss">
.ProfilePicture {
  border: 3px solid #FFF;
  border-radius: 50%;
  width: 80px;
  min-height: 80px;
  overflow: hidden;

  margin-top: -70px;
  background: transparent;

  &__image {
    max-width: 100%;
  }
}
</style>
