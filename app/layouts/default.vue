<script setup lang="ts">
import { ref } from "vue";

const links = [
  { label: "Dashboard", icon: "i-lucide-layout-dashboard", to: "/" },
  { label: "Products", icon: "i-lucide-package", to: "/products" },
  { label: "Orders", icon: "i-lucide-shopping-cart", to: "/orders" },
  { label: "Customers", icon: "i-lucide-users", to: "/customers" },
];

const bottomLinks = [
  { label: "Settings", icon: "i-lucide-settings", to: "/settings" },
];

const userDropdownItems = [
  [
    { label: "Profile", icon: "i-lucide-user" },
    { label: "Billing", icon: "i-lucide-credit-card" },
  ],
  [{ label: "Sign out", icon: "i-lucide-log-out", color: "error" }],
];

const isMobileMenuOpen = ref(false);
</script>

<template>
  <div
    class="h-screen flex overflow-hidden bg-gray-50 dark:bg-gray-950 font-sans text-gray-900 dark:text-gray-100"
  >
    <!-- Mobile sidebar -->
    <USlideover v-model:open="isMobileMenuOpen" side="left" class="lg:hidden">
      <template #content>
        <div class="flex flex-col h-full bg-white dark:bg-gray-900">
          <div
            class="h-16 flex items-center px-6 border-b border-gray-200 dark:border-gray-800 shrink-0"
          >
            <div class="font-bold text-lg flex items-center gap-2">
              <UIcon
                name="i-lucide-croissant"
                class="w-6 h-6 text-primary-500"
              />
              Bakery Admin
            </div>
            <UButton
              icon="i-lucide-x"
              color="neutral"
              variant="ghost"
              class="ml-auto"
              @click="isMobileMenuOpen = false"
            />
          </div>
          <div class="p-4 flex-1 flex flex-col justify-between overflow-y-auto">
            <UNavigationMenu orientation="vertical" :items="links" />
            <UNavigationMenu
              orientation="vertical"
              :items="bottomLinks"
              class="mt-auto"
            />
          </div>
        </div>
      </template>
    </USlideover>

    <!-- Desktop sidebar -->
    <aside
      class="hidden lg:flex w-64 flex-col border-r border-gray-200 dark:border-gray-800 bg-white dark:bg-gray-900 shrink-0 transition-all duration-300"
    >
      <div
        class="h-16 flex items-center px-6 border-b border-gray-200 dark:border-gray-800 shrink-0"
      >
        <NuxtLink
          to="/"
          class="font-bold text-lg flex items-center gap-2 text-gray-900 dark:text-white hover:opacity-80 transition-opacity"
        >
          <UIcon name="i-lucide-croissant" class="w-6 h-6 text-primary-500" />
          Bakery Admin
        </NuxtLink>
      </div>
      <div class="p-4 flex-1 overflow-y-auto">
        <UNavigationMenu orientation="vertical" :items="links" />
      </div>
      <div class="p-4 border-t border-gray-200 dark:border-gray-800 shrink-0">
        <UNavigationMenu orientation="vertical" :items="bottomLinks" />
      </div>
    </aside>

    <!-- Main Content -->
    <div class="flex-1 flex flex-col w-0 overflow-hidden">
      <!-- Top header -->
      <header
        class="h-16 shrink-0 flex items-center justify-between px-4 sm:px-6 lg:px-8 border-b border-gray-200 dark:border-gray-800 bg-white/80 dark:bg-gray-900/80 backdrop-blur-sm z-10"
      >
        <div class="flex items-center gap-4">
          <UButton
            icon="i-lucide-menu"
            color="neutral"
            variant="ghost"
            class="lg:hidden"
            @click="isMobileMenuOpen = true"
            aria-label="Open sidebar"
          />
          <UInput
            icon="i-lucide-search"
            placeholder="Search..."
            color="neutral"
            variant="subtle"
            class="hidden md:flex w-72"
          >
            <template #trailing>
              <UKbd>⌘K</UKbd>
            </template>
          </UInput>
        </div>

        <div class="flex items-center gap-3">
          <UColorModeButton />

          <!-- Separator -->
          <div
            class="w-px h-6 bg-gray-200 dark:bg-gray-800 hidden sm:block"
          ></div>

          <UDropdownMenu :items="userDropdownItems">
            <UButton
              color="neutral"
              variant="ghost"
              class="p-0 hover:bg-transparent"
            >
              <UAvatar
                src="https://avatars.githubusercontent.com/u/739984?v=4"
                alt="Admin User"
                size="sm"
              />
            </UButton>
          </UDropdownMenu>
        </div>
      </header>

      <!-- Page Content -->
      <main class="flex-1 overflow-y-auto focus:outline-none">
        <div class="p-4 sm:p-6 lg:p-8 max-w-7xl mx-auto w-full">
          <slot />
        </div>
      </main>
    </div>
  </div>
</template>
