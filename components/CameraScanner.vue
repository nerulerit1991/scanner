<template>
  <div>
    <video ref="video" width="640" height="480" autoplay></video>
    <button @click="captureImage">Capture Image</button>

    <h1 v-if="scannedText">{{ scannedText }}</h1>
  </div>
</template>

<script>
import Tesseract from 'tesseract.js';

export default {
  data() {
    return {
      video: null,
      scannedText: null,
    };
  },
  mounted() {
    this.initCamera();
  },
  methods: {
    initCamera() {
      navigator.mediaDevices
        .getUserMedia({ video: true })
        .then((stream) => {
          this.video = this.$refs.video;
          this.video.srcObject = stream;
        })
        .catch((error) => {
          console.error('Error accessing camera:', error);
        });
    },
    captureImage() {
      const canvas = document.createElement('canvas');
      const context = canvas.getContext('2d');
      canvas.width = this.video.videoWidth;
      canvas.height = this.video.videoHeight;
      context.drawImage(this.video, 0, 0, canvas.width, canvas.height);

      const capturedImage = new Image();
      capturedImage.src = canvas.toDataURL('image/png');

      this.scanImage(capturedImage);
    },
    scanImage(image) {
      Tesseract.recognize(
        image,
        'eng',
        { logger: (info) => console.log(info) }
      )
        .then(({ data: { text } }) => {
          this.scannedText = text;
        })
        .catch((error) => {
          console.error('Error scanning image:', error);
        });
    },
  },
  beforeDestroy() {
    if (this.video) {
      const stream = this.video.srcObject;
      const tracks = stream.getTracks();

      tracks.forEach((track) => track.stop());
    }
  },
};
</script>

<style scoped>
/* Add your component styles here */
</style>
