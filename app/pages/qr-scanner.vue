<template>
  <div class="d-flex mx-auto align-center justify-center" style="height: 90vh;">
    
    <v-card width="400">
      <v-card-text>
        <!-- Video element for QR Scanner Output -->
         <video class="qr-video" ref="videoRef"></video>

         <v-btn color="primary" @click="startScanner" class="my-3" block>Start Scanner</v-btn>

         <v-btn color="error" @click="stopScanner" block>Stop Scanner</v-btn>
    
      </v-card-text>
    </v-card>

  </div>
</template>

<script lang="ts" setup>
//@ts-nocheck
import QrScanner from 'qr-scanner'

const videoRef = ref<HTMLVideoElement | null>(null)
let scanner: QrScanner | null = null

// Function to start scanner
async function startScanner() {
if (!videoRef.value) return
  scanner = new QrScanner(
    videoRef.value,
    (scanResult) => {
      scanner?.stop()
    },
    {
      preferredCamera: 'environment',
      highlightScanRegion: true,
      highlightCodeOutline: true,
    }
  )

  await scanner.start()
}


// Function to stop scanner
async function stopScanner() {
  await scanner.stop()
}

</script>

<style scoped>
.qr-video {
  width: 100%;
  max-width: 400px;
  border-radius: 12px;
  background: #000;
}
</style>