<script setup lang="ts">
import type { NavItem } from '@nuxt/content/dist/runtime/types'
import type { Link } from '#ui-pro/types'

defineProps<{
  links?: Link[]
}>()

const navigation = inject<Ref<NavItem[]>>('navigation')

const { metaSymbol } = useShortcuts()

const route = useRoute()
const mobileNav = computed(() => {
  const links = mapContentNavigation(navigation.value)
  // Show Migration and Bridge on mobile only when user is reading them
  const docsLink = links.find(link => link.to === '/docs')
  if (docsLink && !route.path.startsWith('/docs/bridge') && !route.path.startsWith('/docs/migration'))
    docsLink.children = docsLink.children?.filter(link => !['/docs/bridge', '/docs/migration'].includes(link.to as string))

  return links
})
</script>

<template>
  <UHeader :links="links">
    <template #logo>
      <NuxtLink to="/" class="flex items-center gap-1.5 text-xl font-bold text-gray-900 dark:text-white" aria-label="Oku">
        <MotionLogo class="h-6 w-auto" />
        Motion
      </NuxtLink>
    </template>

    <template #right>
      <UTooltip text="Search" :shortcuts="[metaSymbol, 'K']">
        <UDocsSearchButton :label="null" />
      </UTooltip>

      <UTooltip :text="$colorMode.preference === 'dark' ? 'Switch to light mode' : 'Switch to dark mode'">
        <UColorModeButton />
      </UTooltip>

      <UTooltip text="GitHub Stars">
        <UButton
          icon="i-simple-icons-github" to="https://github.com/oku-ui/motion" target="_blank"
          label="38"
        />
      </UTooltip>
    </template>

    <template #panel>
      <UNavigationTree :links="mobileNav" default-open :multiple="false" />
    </template>
  </UHeader>
</template>
