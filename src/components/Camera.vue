
<template>
  <div>
    <div style="">
      <video ref="videoElement"></video>
      <h1 v-if="countdown">{{ countdown }}</h1>
        <h1 v-else>{{ counter }}</h1>
      <div style="display: flex; flex-direction: row;">
        <div v-for="(image, index) in images" :key="index" style="margin-right: 10px;">
          <img :src="image.src" :style="{ width: `${imgWidth}px`, height: `${imgHeight}px`, 'object-fit': 'cover' }" />
        </div>
      </div>
    </div>
   
    <button @click="startCapture" v-if="images.length < 4">Start Capture</button>
    <!-- <button @click="toSort" v-else></button> -->
    <button @click="downloadPhotos" v-if="images.length === 4">Download Photos</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      counter: 0,
      images: [],
      captureCount: 0,
      countdown: 0,
      imgWidth: 270,
      imgHeight: 270
    };
  },
  mounted() {
    const video = this.$refs.videoElement;
    navigator.mediaDevices.getUserMedia({ video: true }).then((stream) => {
      video.srcObject = stream;
      video.play();
    });
  },
  methods: {
    startCapture() {
      this.counter = 0;
      this.images = [];
      this.captureCount = 0;
      this.countdown = 4;
      const video = this.$refs.videoElement;
      const interval = setInterval(() => {
        if (this.captureCount < 4) {
          if (this.countdown <= 0) {
            const canvas = document.createElement("canvas");
            canvas.width = this.imgWidth;
            canvas.height = this.imgHeight;

            const context = canvas.getContext("2d");
            context.drawImage(video, 0, 0, canvas.width, canvas.height);

            const img = new Image();
            img.src = canvas.toDataURL();

            this.images.push(img);
            this.captureCount++;

            this.countdown = 4;
          } else {
            this.countdown--;
          }
        } else {
          clearInterval(interval);
        }
      }, 1000);

      // Update counter every second
      let count = 0;
      setInterval(() => {
        if (this.countdown <= 0) {
          count++;
          this.counter = count;
        }
      }, 1000);
    },
      downloadPhotos() {
    this.images.forEach((image, index) => {
      const link = document.createElement("a");
      link.href = image.src;
      link.download = `photo${index + 1}.jpg`;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    });
    }
    
  }
};
</script>