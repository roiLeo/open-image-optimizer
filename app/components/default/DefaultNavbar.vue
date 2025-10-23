<template>
  <header class="bg-background/75">
    <UContainer class="flex h-14 items-center justify-between">
      <NuxtLink to="/">
        <div class="flex items-center gap-3">
          <UIcon name="i-heroicons-photo" class="size-8 text-purple-600 dark:text-purple-400" />
          <h1 class="text-xl font-bold bg-gradient-to-r from-purple-600 to-pink-600 dark:from-purple-400 dark:to-pink-400 bg-clip-text text-transparent">
            Social Media Image Optimizer
          </h1>
        </div>
      </NuxtLink>
      <div class="flex gap-1.5">
        <ColorModeButton />
      </div>
    </UContainer>
  </header>
</template>
