<template>
  <div class="d-flex mx-auto align-center justify-center" style="height: 90vh;">
    <v-card width="400">
      <v-card-text>
        <video class="qr-video" ref="videoRef"></video>

        <v-alert
          v-if="cameraError"
          type="warning"
          variant="tonal"
          class="mt-3"
        >
          {{ cameraError }}
        </v-alert>

        <v-alert
          v-if="scannedData"
          type="success"
          variant="tonal"
          class="mt-3"
        >
          <strong>Scanned Result:</strong> {{ scannedData }}
        </v-alert>

        <v-btn color="primary" @click="startScanner" class="my-3" block>
          Start Scanner
        </v-btn>

        <v-btn color="error" @click="stopScanner" block>
          Stop Scanner
        </v-btn>
      </v-card-text>
    </v-card>
  </div>
</template>

<script lang="ts" setup>
// @ts-nocheck
import QrScanner from 'qr-scanner'

const emit = defineEmits<{
  (e: 'scanned', value: string): void
}>()

const videoRef = ref<HTMLVideoElement | null>(null)
const scannedData = ref('')
const cameraError = ref('')
let scanner: QrScanner | null = null

async function startScanner() {
  if (!videoRef.value) return

  cameraError.value = ''
  scannedData.value = ''

  const hasCamera = await QrScanner.hasCamera()
  if (!hasCamera) {
    cameraError.value = 'No camera detected on this device.'
    return
  }

  try {
    if (!scanner) {
      scanner = new QrScanner(
        videoRef.value,
        (scanResult) => {
          const data = typeof scanResult === 'string' ? scanResult : scanResult.data
          scannedData.value = data
          emit('scanned', data)
          scanner?.stop()
        },
        {
          preferredCamera: 'environment',
          highlightScanRegion: true,
          highlightCodeOutline: true,
        }
      )
    }

    await scanner.start()
  } catch (err) {
    cameraError.value = 'Unable to access the camera. Please allow camera permissions in your browser.'
  }
}

async function stopScanner() {
  if (!scanner) return
  await scanner.stop()
}

onMounted(() => {
  startScanner()
})

onBeforeUnmount(() => {
  scanner?.stop()
  scanner?.destroy()
  scanner = null
})
</script>

<style scoped>
.qr-video {
  width: 100%;
  max-width: 400px;
  border-radius: 12px;
  background: #000;
}
</style>