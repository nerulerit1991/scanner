<template>
  <div>
    <input type="file" @change="handleFileChange" />
    <div v-if="scannedText">
      <h3>Scanned Text:</h3>
      <p>{{ scannedText }}</p>
    </div>
  </div>
</template>

<script>
import Tesseract from 'tesseract.js';

export default {
  data() {
    return {
      scannedText: null,
    };
  },
  methods: {
    async handleFileChange(event) {
      const file = event.target.files[0];

      if (file) {
        try {
          const result = await this.scanImage(file);
          this.scannedText = result.text;
        } catch (error) {
          console.error('Error scanning image:', error);
        }
      }
    },
    scanImage(file) {
      return new Promise((resolve, reject) => {
        Tesseract.recognize(
          file,
          'eng', // Specify the language for OCR
          { logger: (info) => console.log(info) } // Optional logger
        )
          .then(({ data: { text } }) => {
            resolve({ text });
          })
          .catch((error) => {
            reject(error);
          });
      });
    },
  },
};
</script>

<style scoped>
/* Add your component styles here */
</style>
