<template>
  <UApp>
    <div class="flex min-h-screen flex-col bg-gradient-to-br from-purple-50 via-pink-50 to-blue-50 dark:from-purple-950 dark:via-pink-950 dark:to-blue-950">
      <DefaultNavbar />

      <UContainer class="w-full">
        <NuxtPage />
      </UContainer>

      <DefaultFooter />
    </div>
  </UApp>
</template>
