<template>
  <div
    class="w-full max-w-(--ui-container) mx-auto px-4 py-3 sm:px-6 lg:px-8 flex items-center justify-between gap-3 h-full"
  >
    <div>Logo</div>
    <div class="space-x-2 flex justify-items-center">
      <ClientOnly v-if="!colorMode?.forced">
        <UButton
          :icon="isDark ? 'i-lucide-moon' : 'i-lucide-sun'"
          color="neutral"
          variant="ghost"
          @click="isDark = !isDark"
        />

        <template #fallback>
          <div class="size-8" />
        </template>
      </ClientOnly>
      <UButton class="font-bold rounded-full" variant="soft" size="sm">
        Log in</UButton
      >
      <UButton class="font-bold rounded-full" size="sm">Sign up</UButton>
    </div>
  </div>
  <div
    class="w-full max-w-(--ui-container) mx-auto px-4 sm:px-6 lg:px-8 flex flex-col lg:grid py-7 sm:py-10 lg:py-12 gap-16 sm:gap-y-24"
  >
    <slot />
  </div>
  <footer>
    <!---->
    <div
      class="w-full max-w-(--ui-container) mx-auto px-4 sm:px-6 lg:px-8 py-8 lg:py-4 lg:flex lg:items-center lg:justify-between lg:gap-x-3"
    >
      <!--[-->
      <div
        class="lg:flex-1 flex items-center justify-center lg:justify-end gap-x-1.5 lg:order-3"
      >
        <!--[--><!--[--><!--[--><a
          href="https://github.com/nuxt-ui-pro/starter"
          rel="noopener noreferrer"
          target="_blank"
          aria-label="GitHub"
          class="rounded-[calc(var(--ui-radius)*1.5)] font-medium inline-flex items-center disabled:cursor-not-allowed aria-disabled:cursor-not-allowed disabled:opacity-75 aria-disabled:opacity-75 transition-colors text-sm gap-1.5 text-(--ui-text) hover:bg-(--ui-bg-elevated) focus:outline-none focus-visible:bg-(--ui-bg-elevated) hover:disabled:bg-transparent dark:hover:disabled:bg-transparent hover:aria-disabled:bg-transparent dark:hover:aria-disabled:bg-transparent p-1.5"
          ><!--[--><!--[--><span
            class="iconify i-simple-icons:github shrink-0 size-5"
            aria-hidden="true"
            style=""
          ></span
          ><!--]--><!--[--><!----><!--]--><!--[--><!----><!--]--><!--]--></a
        ><!--]--><!--]--><!--]-->
      </div>
      <div class="mt-3 lg:mt-0 lg:order-2 flex items-center justify-center">
        <!--[--><!--]-->
      </div>
      <div
        class="flex items-center justify-center lg:justify-start lg:flex-1 gap-x-1.5 mt-3 lg:mt-0 lg:order-1"
      >
        <!--[-->
        <p class="text-sm text-(--ui-text-muted)">Copyright © 2025</p>
        <!--]-->
      </div>
      <!--]-->
    </div>
    <!---->
  </footer>
</template>

<script lang="ts" setup>
const colorMode = useColorMode();

const isDark = computed({
  get() {
    return colorMode.value === "dark";
  },
  set() {
    colorMode.preference = colorMode.value === "dark" ? "light" : "dark";
  },
});
</script>

<style></style>
