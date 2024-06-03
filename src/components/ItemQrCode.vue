<template>
    <div>
      <h1>Items with QR Codes</h1>
      <button @click="addItem">Add Item</button>
      
      <ul>
        <li v-for="item in items" :key="item.id">
          <p>{{ item.name }}</p>
          <canvas :ref="'canvas-' + item.id"></canvas>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import QRCode from 'qrcode';
  
  export default {
    data() {
      return {
        items: []
      };
    },
    mounted() {
      window.addEventListener('focus', this.restoreQrCodes);
    },
    beforeUnmount() {
      window.removeEventListener('focus', this.restoreQrCodes);
    },
    methods: {
      addItem() {
        const id = this.items.length + 1;
        const item = {
          id: id,
          name: `Item ${id}`,
          qrCodeData: null // To store the QR code data
        };
  
        this.items.push(item);
        this.generateQrCode(item);
      },
      generateQrCode(item) {
        this.$nextTick(() => {
          const canvasRef = 'canvas-' + item.id;
          const canvas = this.$refs[canvasRef][0];
          console.log(canvas);
          console.log(QRCode);
          QRCode.toCanvas(canvas, item.name, { errorCorrectionLevel: 'H' }, (error) => {
            if (error) console.error(error);
            // Save the QR code data to the item
            item.qrCodeData = canvas.toDataURL();
          });
        });
      },
      restoreQrCodes() {
        this.items.forEach(item => {
          if (item.qrCodeData) {
            const canvasRef = 'canvas-' + item.id;
            const canvas = this.$refs[canvasRef][0];
            const ctx = canvas.getContext('2d');
            const img = new Image();
            img.onload = () => {
              ctx.clearRect(0, 0, canvas.width, canvas.height);
              ctx.drawImage(img, 0, 0);
            };
            img.src = item.qrCodeData;
          }
        });
      }
    }
  };
  </script>
  
  <style scoped>
  /* Add any necessary styles here */
  </style>
  