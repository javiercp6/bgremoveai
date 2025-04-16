<template>
  <div
    @dragenter.prevent="toggle_active()"
    @dragleave.prevent="toggle_active()"
    @dragover.prevent
    @drop.prevent="drop"
    :class="{
      'border-dashed  border-gray-300': !originalImageUrl,
      'border-green-500': dragActive,
    }"
    class="flex h-96 items-center justify-center w-full max-w-4xl border-4 rounded dark:border-2"
    style="padding: 0px"
  >
    <div
      v-if="!originalImageUrl"
      class="flex flex-col items-center justify-center gap-2 text-gray-500 dark:text-gray-100"
    >
      <span>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          :class="{ 'animate-bounce': droppedFile === null }"
          class="w-16 h-16"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"
          />
        </svg>
      </span>
      <p class="text-base md:text-xl font-semibold">Drag a file here</p>
      <p class="text-xs md:text-sm font-semibold dark:text-gray-400">
        Or if you prefer
      </p>
      <!-- File input -->

      <input
        type="file"
        ref="fileInputRef"
        accept="image/*"
        @change="selectedFile"
        name="file"
        id="file"
        class="hidden"
      />

      <UButton class="font-bold rounded-full" @click="triggerFileInput"
        >Upload Image</UButton
      >
    </div>
    <div
      v-if="originalImageUrl && !resultImageUrl"
      class="relative overflow-hidden"
    >
      <img
        :src="originalImageUrl"
        class="h-[380px] w-full object-contain"
        alt=""
      />

      <div class="absolute inset-0 bg-black/50"></div>
      <!-- Capa mágica interactiva -->
      <div class="absolute inset-0 flex items-center justify-center space-x-4">
        <!-- Efecto de barrido mágico -->
        <div
          class="absolute inset-0 bg-gradient-to-r from-transparent via-white/30 to-transparent animate-shine"
        ></div>

        <span class="text-white text-lg font-bold relative animate-float">
          Processing image...
        </span>
      </div>
    </div>

    <client-only v-if="originalImageUrl && resultImageUrl">
      <ImgComparisonSlider>
        <!-- eslint-disable -->
        <img
          slot="first"
          class="h-[378px] object-contain"
          style="width: 100%"
          :src="originalImageUrl"
        />
        <img
          slot="second"
          class="h-[378px] object-contain"
          style="width: 100%"
          :src="resultImageUrl || originalImageUrl"
        />
        <div slot="handle" class="flex gap-2">
          <div class="bg-black/50 rounded-full">
            <UIcon slot="handle" name="i-lucide-chevron-left" class="size-5" />
          </div>
          <div class="bg-black/50 rounded-full">
            <UIcon slot="handle" name="i-lucide-chevron-right" class="size-5" />
          </div>
        </div>
      </ImgComparisonSlider>
    </client-only>
  </div>
</template>

<script setup lang="ts">
import { ImgComparisonSlider } from "@img-comparison-slider/vue";
const props = defineProps<{
  originalImageUrl: string | null;
  resultImageUrl: string | null;
  onCancel: boolean;
}>();

const { originalImageUrl, resultImageUrl, onCancel } = toRefs(props);
const emit = defineEmits(["pass-image", "clear-image"]);
const dragActive = ref(false);
const droppedFile = ref<any>(null);

watch(onCancel, (newValue) => {
  if (newValue) {
    clearDropped();
  }
  /*  if (newStatus === "error") {
    emit("error");
  } */
});

const toggle_active = () => {
  if (droppedFile.value == null) {
    dragActive.value = !dragActive.value;
  }
};

const drop = (event: any) => {
  console.log("drop");
  droppedFile.value = event.dataTransfer.files[0];
  emit("pass-image", event.dataTransfer.files[0]);
  /* emit("pass-image", event); */
  dragActive.value = true;
};

const selectedFile = (event: any) => {
  console.log("selectedFile");
  droppedFile.value = event.target.files[0];
  emit("pass-image", event.target.files[0]);
  /* emit("pass-image", event); */
  dragActive.value = true;
};

const clearDropped = () => {
  console.log("clearDropped");
  droppedFile.value = null;
  /* document.getElementById("file").value = ""; */
  /* emit("clear-image"); */
  if (fileInputRef.value) {
    fileInputRef.value.value = ""; // Esto limpia el valor del input
  }
  dragActive.value = false;
};

const fileInputRef = ref<HTMLInputElement | null>(null);
const triggerFileInput = () => {
  // Asegúrate de que la referencia al input existe antes de llamar a click()
  fileInputRef.value?.click();
};
</script>

<style scoped>
@keyframes shine {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}

@keyframes wand-pulse {
  0%,
  100% {
    transform: translateY(0) rotate(0deg);
  }
  50% {
    transform: translateY(-10px) rotate(15deg);
  }
}

@keyframes particle-spray {
  0% {
    transform: scale(0);
    opacity: 1;
  }
  100% {
    transform: scale(1);
    opacity: 0;
  }
}

@keyframes float {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

.animate-shine {
  animation: shine 2s infinite;
  mask-image: linear-gradient(75deg, rgba(0, 0, 0, 0.8) 30%, transparent 70%);
  mix-blend-mode: screen;
}

.animate-wand-pulse {
  animation: wand-pulse 1.5s ease-in-out infinite;
  filter: drop-shadow(0 0 8px rgba(255, 255, 255, 0.5));
}

.animate-particle-spray {
  animation: particle-spray 1s infinite;
}

.animate-float {
  animation: float 2s ease-in-out infinite;
}
</style>
