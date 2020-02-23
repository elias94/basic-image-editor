<template>
  <div class="PageContent">
    <Card>
      <label for="brightness">Brightness</label>
      <Slider
        name="brightness"
        v-model="brightness"
        v-on:mousemove="onCorrectionChange"
      />
    </Card>
    <Card>
      <label for="contrast">Contrast</label>
      <Slider
        name="contrast"
        v-model="contrast"
        v-on:mousemove="onCorrectionChange"
      />
    </Card>
    <Card>
      <div class="canvas__container">
        <canvas class="canvas__element" ref="canvas" />
      </div>
      <ImagePicker
        buttonText="UPLOAD"
        inputText="NAME"
        :fileName="fileName"
        v-on:file-upload="onImageSelection"
      />
    </Card>
  </div>
</template>

<script>
import Card from '../atoms/Card.vue'
import Slider from '../atoms/Slider.vue'
import ImagePicker from '../molecules/ImagePicker.vue'

// Truncate out-of-range value 0-255 for RGB pixels
const truncate = (value) => {
  if (value > 255) {
    return 255
  } else if (value < 0) {
    return 0
  }
  return value
}

// Extract the height padding to apply to the canvas rendering.
// Uses the difference between the actual canvas size
// and the visible portion inside the parent container
const canvasImgPadding = (img, canvas) => {
  const { width: imgWidth, height: imgHeight } = img;
  const parentWidth = canvas.parentNode.offsetWidth;
  const parentHeight= canvas.parentNode.offsetHeight;
  
  if (imgWidth < parentWidth) {
    return 0;
  }

  const canvasHeight = (parentWidth / imgWidth) * imgHeight;
  const padding = (canvasHeight - parentHeight) / 2;
  return canvasHeight > parentHeight ? padding || 0 : 0;
}

export default {
  name: 'PageContent',
  components: {
    Card,
    Slider,
    ImagePicker,
  },
  data() {
    return {
      initialImageData: null,
      brightness: '0',
      contrast: '0',
      fileName: '',
    };
  },
  props: {
    msg: String
  },
  methods: {
    onImageSelection(imgFile) {
      const reader = new FileReader();
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');
      this.fileName = imgFile.name;

      reader.onload = (event) => {
        var img = new Image();
        img.onload = () => {
          // get padding to center image inside visible canvas
          const padding = canvasImgPadding(img, canvas);

          // set size proportional to image
          canvas.width = img.width;
          canvas.height = img.height;
          
          // draw image full-width and adapted height
          ctx.drawImage(img, 0, -padding, img.width, img.height);

          // save initial image pixels data for future transformations
          const imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);
          this.initialImageData = imgData;
        }
        img.src = event.target.result;
      };
      reader.readAsDataURL(imgFile);
    },

    onCorrectionChange() {
      // source:
      //    https://www.dfstudios.co.uk/articles/programming/image-programming-algorithms/image-processing-algorithms-part-4-brightness-adjustment/
      //    https://www.dfstudios.co.uk/articles/programming/image-programming-algorithms/image-processing-algorithms-part-5-contrast-adjustment/

      const { brightness: strBrightness, contrast: strContrast, initialImageData } = this;
      const brightness = parseInt(strBrightness, 10);
      const contrast = parseInt(strContrast, 10);
      const ctx = this.$refs.canvas.getContext('2d');

      // Clone initial ImageData
      const imageData = new ImageData(
        new Uint8ClampedArray(initialImageData.data),
        initialImageData.width,
        initialImageData.height
      )
      const { data } = imageData;
      const factor = (259 * (contrast + 255)) / (255 * (259 - contrast));

      // Set the image constrast + brightness
      for (let i = 0; i < data.length; i+= 4) {
        data[i] = truncate(factor * (data[i] - 128) + 128 + brightness);
        data[i+1] = truncate(factor * (data[i+1] - 128) + 128 + brightness);
        data[i+2] = truncate(factor * (data[i+2] - 128) + 128 + brightness);
      }

      ctx.putImageData(imageData, 0, 0);
    },
  },
}
</script>

<style lang="scss">
.canvas {
  &__container {
    width: 100%;
    height: calc(vw * .6);
    overflow: hidden
  }

  &__element {
    width: 100%;
  }
}
</style>
