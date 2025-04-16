<template>
  <div>
    <div class="text-center mb-9">
      <!---->
      <h1
        class="text-4xl sm:text-6xl text-pretty tracking-tight font-bold text-(--ui-text-highlighted)"
      >
        Remove image background with AI
      </h1>
      <div
        class="text-lg sm:text-xl/8 text-(--ui-text-muted) text-balance mt-2"
      >
        Background removal in seconds: No loss of quality, AI-powered. #1
        Against Competitors
        <!-- Background removal in seconds: No loss of quality, AI-powered. Outperforms the competition: Faster and more accurate! -->
        <!-- Zero Quality Compromises. #1 Against Competitors. -->
      </div>
    </div>
    <div class="flex justify-center flex-col gap-3">
      <div class="flex justify-center">
        <div
          class="w-full max-w-4xl flex justify-end gap-2"
          style="padding: 0px"
        >
          <UButton
            class="font-bold rounded-full"
            size="sm"
            color="error"
            variant="soft"
            @click="cancel"
            :disabled="!resultImageUrl"
            >Cancel</UButton
          >
          <UButton
            class="font-bold rounded-full"
            size="sm"
            color="primary"
            @click="downloadResultImage"
            :disabled="!resultImageUrl"
            >Download</UButton
          >
        </div>
      </div>
      <div class="flex justify-center">
        <DropZone
          @pass-image="handleImageUpload"
          :originalImageUrl="originalImageUrl"
          :resultImageUrl="resultImageUrl"
          :onCancel="onCancel"
        ></DropZone>
      </div>
      <p v-if="error" style="color: red">Error: {{ error }}</p>

      <!-- <div
        v-if="originalImageUrl || resultImageUrl"
        style="display: flex; gap: 20px; margin-top: 20px"
      >
        <div v-if="originalImageUrl">
          <h2>Original</h2>
          <img
            :src="originalImageUrl"
            alt="Original"
            style="max-width: 300px; max-height: 300px"
          />
        </div>
  
        <div v-if="resultImageUrl">
          <h2>Resultado (Sin Fondo)</h2>
          <img
            :src="resultImageUrl"
            alt="Resultado sin fondo"
            style="max-width: 300px; max-height: 300px"
          />
        </div>
      </div> -->
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onBeforeUnmount } from "vue";
// Importa la función principal de la biblioteca
import { removeBackground } from "@imgly/background-removal";

// --- Estados Reactivos ---
const originalImageUrl = ref<string | null>(null); // URL para mostrar la imagen original
const resultImageUrl = ref<string | null>(null); // URL para mostrar la imagen resultante
const isLoading = ref<boolean>(false); // Estado de carga
const error = ref<string | null>(null); // Mensaje de error
const onCancel = ref(false);
const nameImage = ref("");
let currentResultBlobUrl: string | null = null; // Para limpiar la URL del Blob

// --- Manejador de Carga de Imagen ---
const handleImageUpload = async (file: File) => {
  /* const target = event.target as HTMLInputElement;
  const file = target.files?.[0];
 */
  if (!file) {
    return;
  }

  // Reiniciar estados
  isLoading.value = true;
  onCancel.value = false;
  nameImage.value = "";
  error.value = null;
  if (currentResultBlobUrl) {
    URL.revokeObjectURL(currentResultBlobUrl); // Limpia la URL anterior
    currentResultBlobUrl = null;
  }
  resultImageUrl.value = null;

  // Mostrar vista previa de la imagen original
  const reader = new FileReader();
  reader.onload = (e) => {
    originalImageUrl.value = e.target?.result as string;
  };
  reader.readAsDataURL(file);

  // --- Procesamiento con Imgly ---
  try {
    // Llama a la función removeBackground
    // Debes configurar 'publicPath' para que apunte a la carpeta donde pusiste los assets
    // Asegúrate de que esta ruta sea correcta según dónde colocaste los assets en tu carpeta `public`
    const blob = await removeBackground(file, {
      publicPath: "https://bgremoveia.netlify.app/assets/imgly/", // <-- ¡IMPORTANTE! Ajusta esta ruta
      // Otras opciones de configuración (opcional):
      // progress: (key, current, total) => {
      //   console.log(`Descargando ${key}: ${current} de ${total}`);
      // },
      // output: {
      //   format: 'image/png' // Puedes especificar el formato de salida
      // }
    });

    // Crea una URL para el Blob resultante
    currentResultBlobUrl = URL.createObjectURL(blob);
    resultImageUrl.value = currentResultBlobUrl;
    const rawFileName = file.name;
    nameImage.value =
      rawFileName.lastIndexOf(".") !== -1
        ? rawFileName.substring(0, rawFileName.lastIndexOf("."))
        : rawFileName;
  } catch (err: any) {
    console.error("Error al eliminar el fondo:", err);
    error.value = err.message || "Ocurrió un error desconocido.";
  } finally {
    isLoading.value = false;
  }
};

const downloadResultImage = () => {
  if (!resultImageUrl.value) {
    error.value = "No hay imagen para descargar";
    return;
  }

  const link = document.createElement("a");
  link.href = resultImageUrl.value;
  link.download = `RgRemoveAi-${nameImage.value}.png`; // Nombre dinámico
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
};

const cancel = () => {
  // Reset all states to their initial values
  originalImageUrl.value = null;
  resultImageUrl.value = null;
  error.value = null;
  nameImage.value = "";
  // Revoke the current result Blob URL if it exists
  if (currentResultBlobUrl) {
    URL.revokeObjectURL(currentResultBlobUrl);
    currentResultBlobUrl = null;
  }
  onCancel.value = true;
  /* onCancel.value = false; */
};

// --- Limpieza ---
// Es buena práctica revocar las URLs de los Blobs cuando el componente se destruye
// para liberar memoria.
onBeforeUnmount(() => {
  if (currentResultBlobUrl) {
    URL.revokeObjectURL(currentResultBlobUrl);
  }
  // Si también creaste una URL de objeto para originalImageUrl (no es el caso aquí, usamos DataURL),
  // también deberías revocarla.
});
</script>

<style scoped>
/* Agrega algo de estilo si lo deseas */

/* .custom-animated-handle {
    transition: transform 0.2s;
  }
  
  .slider-with-animated-handle:hover .custom-animated-handle {
    transform: scale(1.2);
  } */
</style>
