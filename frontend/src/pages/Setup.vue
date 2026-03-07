<template>
  <div class="flex h-screen w-full overflow-hidden sm:bg-surface-menu-bar">
    <div class="w-full overflow-auto">
      <div class="relative h-full">
        <div class="relative z-10 mx-auto pt-8 sm:w-max sm:pt-20">
          <div
            class="flex flex-col items-center"
            @dblclick="window.location.href = '/f-login'"
          >
            <FrappeDriveLogo class="inline-block h-12 w-12 rounded-md" />
          </div>
          <div
            class="mx-auto w-full bg-surface-white px-4 py-8 sm:mt-6 sm:w-112 sm:rounded-2xl sm:px-6 sm:py-6 sm:shadow-2xl"
          >
            <div class="mb-7.5 text-center">
              <p class="mb-2 text-2xl font-semibold leading-6 text-ink-gray-9">
                Welcome to Drive
              </p>
              <p
                class="break-words text-base font-normal leading-[21px] text-ink-gray-7"
              >
                We're glad you're here.
              </p>
            </div>

            <div class="flex flex-col py-5 gap-3">
              <LoadingIndicator class="size-5 self-center" />
              <div class="text-sm text-center">Just a minute...</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { createResource, FormControl, LoadingIndicator } from 'frappe-ui'
import { ref, computed, onMounted } from 'vue'
import FrappeDriveLogo from '@/components/FrappeDriveLogo.vue'
import { useRoute } from 'vue-router'
import { createTeam } from '@/resources/permissions'

const route = useRoute()
const dest = route.query['redirect-to'] || '/drive'

onMounted(() => {
  // Try to create personal team (no-op if already exists server-side)
  createTeam.submit(
    { personal: 1 },
    {
      onSuccess: () => {
        window.location.replace(dest)
      },
      onError: (err) => {
        // Team may already exist (created during signup) — just redirect
        console.warn('createTeam error (may already exist):', err)
        window.location.replace(dest)
      },
    }
  )

  // Safety timeout: if API hangs for >8s, redirect anyway
  setTimeout(() => {
    window.location.replace(dest)
  }, 8000)
})
</script>
