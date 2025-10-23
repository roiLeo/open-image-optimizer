<template>
  <UContainer class="py-8">
    <div class="grid lg:grid-cols-2 gap-6">
      <div class="space-y-6">
        <!-- Upload Area -->
        <UCard>
          <template #header>
            <h2 class="text-xl font-semibold text-neutral-800 dark:text-neutral-300">Upload Image</h2>
          </template>

          <UFileUpload
            v-model="selectedFile"
            icon="i-lucide-image"
            label="Click to upload or drop your image here"
            description="SVG, PNG, JPG, GIF or WebP (max. 10MB)"
            class="min-h-48"
            accept="image/*"
            @change="handleFileSelect"
          />

          <UAlert
            v-if="selectedFile"
            icon="i-heroicons-check-circle"
            color="primary"
            variant="soft"
            :title="selectedFile.name"
            class="mt-4"
          />
        </UCard>

        <!-- Platform Selection -->
        <UCard>
          <template #header>
            <h2 class="text-xl font-semibold text-neutral-800 dark:text-neutral-300">Select Platform</h2>
          </template>

          <div class="grid grid-cols-2 gap-3">
            <UButton
              v-for="(specs, name) in socialPlatforms"
              :key="name"
              :variant="selectedPlatform === name ? 'solid' : 'outline'"
              :color="selectedPlatform === name ? 'primary' : 'neutral'"
              @click="selectedPlatform = name"
              class="h-auto py-3 justify-start"
              block
            >
              <div class="text-left w-full">
                <div class="font-semibold">{{ name }}</div>
                <div class="text-xs opacity-75">
                  {{ specs.width }} × {{ specs.height }}
                </div>
                <div class="text-xs opacity-60">{{ specs.ratio }}</div>
              </div>
            </UButton>
          </div>
        </UCard>

        <!-- Quality Settings -->
        <UCard>
          <template #header>
            <h2 class="text-xl font-semibold text-neutral-800 dark:text-neutral-300">Quality Settings</h2>
          </template>

          <div class="space-y-4">
            <div class="flex justify-between items-center">
              <label class="text-neutral-700 dark:text-neutral-400 font-medium">Quality</label>
              <UBadge color="primary" variant="soft" size="lg">{{ quality }}%</UBadge>
            </div>
            <USlider color="neutral" :min="50" v-model="quality" />
            <div class="flex justify-between text-xs text-neutral-500">
              <span>Smaller file</span>
              <span>Best quality</span>
            </div>
          </div>
        </UCard>

        <!-- Optimize Button -->
        <UButton
          @click="optimizeImage"
          :disabled="!previewUrl || isProcessing"
          :loading="isProcessing"
          icon="i-heroicons-photo"
          size="xl"
          color="primary"
          block
        >
          {{ isProcessing ? 'Processing...' : 'Optimize Image' }}
        </UButton>
      </div>

      <div class="space-y-6">
        <!-- Preview -->
        <UCard>
          <template #header>
            <h2 class="text-xl font-semibold text-neutral-800 dark:text-neutral-300">Preview</h2>
          </template>

          <div class="aspect-square bg-neutral-100 dark:bg-neutral-800 rounded-xl flex items-center justify-center overflow-hidden">
            <img
              v-if="optimizedUrl || previewUrl"
              :src="optimizedUrl || previewUrl"
              :alt="optimizedUrl ? 'Optimized' : 'Original'"
              class="w-full h-full object-cover"
            />
            <div v-else class="text-center">
              <UIcon name="i-heroicons-computer-desktop" class="w-16 h-16 mx-auto text-neutral-300 mb-2" />
              <p class="text-neutral-400">No image uploaded</p>
            </div>
          </div>
        </UCard>

        <!-- Statistics -->
        <UCard v-if="optimizedUrl">
          <template #header>
            <h2 class="text-xl font-semibold text-neutral-800 dark:text-neutral-300">Optimization Results</h2>
          </template>

          <div class="space-y-3">
            <div class="flex justify-between items-center p-3 bg-neutral-50 rounded-lg">
              <span class="text-neutral-600">Original Size</span>
              <span class="font-bold text-neutral-800">{{ formatFileSize(originalSize) }}</span>
            </div>
            <div class="flex justify-between items-center p-3 bg-neutral-50 rounded-lg">
              <span class="text-neutral-600">Optimized Size</span>
              <span class="font-bold text-neutral-800">{{ formatFileSize(optimizedSize) }}</span>
            </div>
            <div class="flex justify-between items-center p-3 bg-green-50 rounded-lg border border-green-200">
              <span class="text-green-800 font-medium">Size Reduction</span>
              <span class="font-bold text-green-700 text-lg">{{ savingsPercent }}%</span>
            </div>
            <div class="flex justify-between items-center p-3 bg-purple-50 rounded-lg border border-purple-200">
              <span class="text-purple-800 font-medium">Dimensions</span>
              <span class="font-bold text-purple-700">
                {{ socialPlatforms[selectedPlatform].width }} × {{ socialPlatforms[selectedPlatform].height }}
              </span>
            </div>
          </div>
        </UCard>

        <!-- Download Button -->
        <UButton
          v-if="optimizedUrl"
          @click="downloadImage"
          icon="i-heroicons-arrow-down-tray"
          size="xl"
          block
        >
          Download Optimized Image
        </UButton>

        <UAlert
          icon="i-heroicons-information-circle"
          color="info"
          variant="soft"
          title="Pro Tips"
        >
          <template #description>
            <ul class="text-sm space-y-1 mt-2 list-disc">
              <li>Higher quality = larger file size</li>
              <li>Images are cropped to fit the aspect ratio</li>
              <li>Use 85% quality for the best balance</li>
              <li>JPEG format works best for photos</li>
            </ul>
          </template>
        </UAlert>
      </div>
    </div>
  </UContainer>
</template>

<script setup lang="ts">
const socialPlatforms = {
  'Instagram Post': { width: 1080, height: 1080, ratio: '1:1' },
  'Instagram Story': { width: 1080, height: 1920, ratio: '9:16' },
  'Facebook Post': { width: 1200, height: 630, ratio: '1.91:1' },
  'Twitter Post': { width: 1200, height: 675, ratio: '16:9' },
  'LinkedIn Post': { width: 1200, height: 627, ratio: '1.91:1' },
  'YouTube Thumbnail': { width: 1280, height: 720, ratio: '16:9' },
  'Pinterest Pin': { width: 1000, height: 1500, ratio: '2:3' },
  'TikTok': { width: 1080, height: 1920, ratio: '9:16' },
}

const selectedFile = ref<File>()
const selectedPlatform = ref('Instagram Post')
const quality = ref(85)
const previewUrl = ref(null)
const optimizedUrl = ref<string|null>(null)
const originalSize = ref(0)
const optimizedSize = ref(0)
const isProcessing = ref(false)

const savingsPercent = computed(() => {
  if (originalSize.value > 0 && optimizedSize.value > 0) {
    return Math.round(((originalSize.value - optimizedSize.value) / originalSize.value) * 100)
  }
  return 0
})

const handleFileSelect = () => {
  const file = selectedFile.value
  if (file && file.type.startsWith('image/')) {
    selectedFile.value = file
    originalSize.value = file.size
    const reader = new FileReader()
    reader.onload = (e) => {
      previewUrl.value = e.target.result
    }
    reader.readAsDataURL(file)
    optimizedUrl.value = null
  }
}

const optimizeImage = () => {
  if (!previewUrl.value) return

  isProcessing.value = true
  const img = new Image()
  img.onload = () => {
    const canvas = document.createElement('canvas')
    const ctx = canvas.getContext('2d')
    const platform = socialPlatforms[selectedPlatform.value]

    canvas.width = platform.width
    canvas.height = platform.height

    const imgRatio = img.width / img.height
    const targetRatio = platform.width / platform.height

    let drawWidth, drawHeight, offsetX = 0, offsetY = 0

    if (imgRatio > targetRatio) {
      drawHeight = platform.height
      drawWidth = img.width * (platform.height / img.height)
      offsetX = (platform.width - drawWidth) / 2
    } else {
      drawWidth = platform.width
      drawHeight = img.height * (platform.width / img.width)
      offsetY = (platform.height - drawHeight) / 2
    }

    ctx.fillStyle = '#ffffff'
    ctx.fillRect(0, 0, canvas.width, canvas.height)
    ctx.drawImage(img, offsetX, offsetY, drawWidth, drawHeight)

    canvas.toBlob(
      (blob) => {
        const url = URL.createObjectURL(blob)
        optimizedUrl.value = url
        optimizedSize.value = blob.size
        isProcessing.value = false
      },
      'image/jpeg',
      quality.value / 100
    )
  }
  img.src = previewUrl.value
}

const downloadImage = () => {
  if (!optimizedUrl.value) return
  const a = document.createElement('a')
  a.href = optimizedUrl.value
  a.download = `${selectedPlatform.value.replace(/\s+/g, '-').toLowerCase()}-optimized.jpg`
  a.click()
}

const formatFileSize = (bytes: number) => {
  if (bytes === 0) return '0 Bytes'
  const k = 1024
  const sizes = ['Bytes', 'KB', 'MB']
  const i = Math.floor(Math.log(bytes) / Math.log(k))
  return Math.round(bytes / Math.pow(k, i) * 100) / 100 + ' ' + sizes[i]
}
</script>
