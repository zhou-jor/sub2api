<template>
  <!-- Custom Home Content: Full Page Mode -->
  <div v-if="homeContent" class="min-h-screen">
    <!-- iframe mode -->
    <iframe
      v-if="isHomeContentUrl"
      :src="homeContent.trim()"
      class="h-screen w-full border-0"
      allowfullscreen
    ></iframe>
    <!-- HTML mode -->
    <div v-else v-html="homeContent"></div>
  </div>

  <!-- Default Home Page -->
  <div
    v-else
    class="relative flex min-h-screen flex-col overflow-hidden bg-gradient-to-br from-red-50/40 via-orange-50/20 to-white dark:from-dark-950 dark:via-dark-900 dark:to-dark-950"
  >
    <!-- Background Decorations -->
    <div class="pointer-events-none absolute inset-0 overflow-hidden">
      <div
        class="absolute -right-40 -top-40 h-96 w-96 rounded-full bg-primary-400/15 blur-3xl"
      ></div>
      <div
        class="absolute -bottom-40 -left-40 h-96 w-96 rounded-full bg-orange-400/10 blur-3xl"
      ></div>
      <div
        class="absolute left-1/3 top-1/4 h-72 w-72 rounded-full bg-amber-300/10 blur-3xl"
      ></div>
    </div>

    <!-- Header -->
    <header class="relative z-20 px-6 py-4">
      <nav class="mx-auto flex max-w-6xl items-center justify-between">
        <div class="flex items-center gap-3">
          <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-gradient-to-br from-primary-500 to-primary-600 shadow-md">
            <span class="text-sm font-bold text-white">AI</span>
          </div>
          <span class="text-lg font-semibold text-gray-900 dark:text-white">{{ siteName }}</span>
        </div>

        <div class="flex items-center gap-3">
          <LocaleSwitcher />
          <button
            @click="toggleTheme"
            class="rounded-lg p-2 text-gray-500 transition-colors hover:bg-gray-100 hover:text-gray-700 dark:text-dark-400 dark:hover:bg-dark-800 dark:hover:text-white"
          >
            <Icon v-if="isDark" name="sun" size="md" />
            <Icon v-else name="moon" size="md" />
          </button>
          <router-link
            v-if="isAuthenticated"
            :to="dashboardPath"
            class="inline-flex items-center gap-1.5 rounded-full bg-primary-500 px-4 py-2 text-xs font-medium text-white transition-colors hover:bg-primary-600"
          >
            {{ t('home.dashboard') }}
          </router-link>
          <template v-else>
            <router-link
              to="/login"
              class="inline-flex items-center rounded-full border border-primary-500 px-4 py-2 text-xs font-medium text-primary-600 transition-colors hover:bg-primary-50 dark:text-primary-400"
            >
              {{ t('home.login') }}
            </router-link>
            <router-link
              to="/register"
              class="inline-flex items-center rounded-full bg-primary-500 px-4 py-2 text-xs font-medium text-white transition-colors hover:bg-primary-600"
            >
              注册
            </router-link>
          </template>
        </div>
      </nav>
    </header>

    <!-- Hero Section -->
    <main class="relative z-10 flex-1 px-6 py-16">
      <div class="mx-auto max-w-6xl">
        <div class="mb-16 text-center">
          <h1
            class="mb-4 text-4xl font-bold text-gray-900 dark:text-white md:text-5xl lg:text-6xl"
          >
            {{ siteName }}
          </h1>
          <p class="mb-8 text-lg text-gray-600 dark:text-dark-300 md:text-xl">
            {{ siteSubtitle }}
          </p>

          <div class="flex flex-wrap items-center justify-center gap-4">
            <router-link
              :to="isAuthenticated ? dashboardPath : '/register'"
              class="btn btn-primary px-8 py-3 text-base shadow-lg shadow-primary-500/30"
            >
              {{ isAuthenticated ? t('home.goToDashboard') : '立即开始' }}
              <Icon name="arrowRight" size="md" class="ml-2" :stroke-width="2" />
            </router-link>
          </div>
        </div>

        <!-- Stats Section -->
        <div class="mb-16 grid grid-cols-2 gap-6 md:grid-cols-4">
          <div v-for="(stat, idx) in stats" :key="idx"
            class="rounded-2xl border border-gray-100 bg-white/80 p-6 text-center shadow-sm backdrop-blur-sm transition-all duration-300 hover:-translate-y-1 hover:shadow-md dark:border-dark-700 dark:bg-dark-800/60"
            :style="{ animationDelay: `${idx * 100}ms` }"
          >
            <div class="mb-1 text-2xl font-bold text-primary-500 md:text-3xl">{{ stat.value }}</div>
            <div class="text-sm text-gray-500 dark:text-dark-400">{{ stat.label }}</div>
          </div>
        </div>

        <!-- Features Grid -->
        <div class="mb-16 grid gap-6 md:grid-cols-3">
          <div
            v-for="(feature, idx) in features"
            :key="idx"
            class="group rounded-2xl border border-gray-100 bg-white/70 p-6 backdrop-blur-sm transition-all duration-300 hover:-translate-y-1 hover:shadow-xl hover:shadow-primary-500/5 dark:border-dark-700/50 dark:bg-dark-800/60"
          >
            <div
              class="mb-4 flex h-12 w-12 items-center justify-center rounded-xl shadow-lg transition-transform group-hover:scale-110"
              :class="feature.iconBg"
            >
              <Icon :name="feature.icon" size="lg" class="text-white" />
            </div>
            <h3 class="mb-2 text-lg font-semibold text-gray-900 dark:text-white">
              {{ feature.title }}
            </h3>
            <p class="text-sm leading-relaxed text-gray-600 dark:text-dark-400">
              {{ feature.desc }}
            </p>
          </div>
        </div>

        <!-- Models Section -->
        <div class="mb-16">
          <div class="mb-8 text-center">
            <h2 class="mb-3 text-2xl font-bold text-gray-900 dark:text-white md:text-3xl">
              支持模型一览
            </h2>
            <p class="text-sm text-gray-600 dark:text-dark-400">
              兼容 OpenAI / Anthropic / Google 等主流 API 格式，按需调用
            </p>
          </div>

          <!-- Model Category Tabs -->
          <div class="mb-6 flex flex-wrap items-center justify-center gap-2">
            <button
              v-for="cat in modelCategories"
              :key="cat.id"
              @click="activeModelCat = cat.id"
              class="rounded-full px-4 py-2 text-sm font-medium transition-all duration-200"
              :class="activeModelCat === cat.id
                ? 'bg-primary-500 text-white shadow-md shadow-primary-500/30'
                : 'bg-white text-gray-600 hover:bg-gray-50 border border-gray-200 dark:bg-dark-800 dark:text-dark-300 dark:border-dark-700'"
            >
              {{ cat.name }}
            </button>
          </div>

          <!-- Model Cards Grid -->
          <div class="grid grid-cols-1 gap-4 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4">
            <div
              v-for="(model, idx) in filteredModels"
              :key="model.id"
              class="group relative overflow-hidden rounded-xl border border-gray-100 bg-white p-4 transition-all duration-300 hover:-translate-y-0.5 hover:border-primary-200 hover:shadow-lg dark:border-dark-700 dark:bg-dark-800"
              :style="{ animationDelay: `${idx * 50}ms` }"
            >
              <div class="mb-3 flex items-center gap-3">
                <div
                  class="flex h-9 w-9 items-center justify-center rounded-lg text-xs font-bold text-white"
                  :class="model.color"
                >
                  {{ model.abbr }}
                </div>
                <div class="flex-1 min-w-0">
                  <div class="truncate text-sm font-medium text-gray-900 dark:text-white">{{ model.name }}</div>
                  <div class="text-xs text-gray-500 dark:text-dark-400">{{ model.provider }}</div>
                </div>
              </div>
              <div class="flex items-center justify-between">
                <span class="rounded-full bg-emerald-50 px-2 py-0.5 text-[10px] font-medium text-emerald-600 dark:bg-emerald-900/30 dark:text-emerald-400">可用</span>
                <span class="text-xs text-gray-400">{{ model.context }}</span>
              </div>
              <div class="absolute inset-0 rounded-xl border-2 border-transparent transition-colors group-hover:border-primary-200/50"></div>
            </div>
          </div>

          <div class="mt-6 text-center">
            <router-link
              :to="isAuthenticated ? dashboardPath : '/register'"
              class="inline-flex items-center gap-2 text-sm font-medium text-primary-500 transition-colors hover:text-primary-600"
            >
              查看全部可用模型
              <Icon name="arrowRight" size="sm" />
            </router-link>
          </div>
        </div>
      </div>
    </main>

    <!-- Footer -->
    <footer class="relative z-10 border-t border-gray-200/50 bg-white/50 px-6 py-8 backdrop-blur-sm dark:border-dark-800/50 dark:bg-dark-900/50">
      <div class="mx-auto flex max-w-6xl flex-col items-center justify-center gap-3 text-center">
        <p class="text-sm text-gray-500 dark:text-dark-400">
          &copy; {{ currentYear }} {{ siteName }}. All Rights Reserved.
        </p>
        <p class="text-xs text-gray-400 dark:text-dark-500">
          <a
            href="https://beian.miit.gov.cn/"
            target="_blank"
            rel="noopener noreferrer"
            class="transition-colors hover:text-gray-600 dark:hover:text-dark-300"
          >京ICP备2026025074号-1</a>
        </p>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore, useAppStore } from '@/stores'
import LocaleSwitcher from '@/components/common/LocaleSwitcher.vue'
import Icon from '@/components/icons/Icon.vue'

const { t } = useI18n()

const authStore = useAuthStore()
const appStore = useAppStore()

const siteName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'AI API 服务平台')
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || '稳定高效的 AI 模型接口服务')
const homeContent = computed(() => appStore.cachedPublicSettings?.home_content || '')

const isHomeContentUrl = computed(() => {
  const content = homeContent.value.trim()
  return content.startsWith('http://') || content.startsWith('https://')
})

const isDark = ref(document.documentElement.classList.contains('dark'))
const isAuthenticated = computed(() => authStore.isAuthenticated)
const isAdmin = computed(() => authStore.isAdmin)
const dashboardPath = computed(() => isAdmin.value ? '/admin/dashboard' : '/dashboard')
const currentYear = computed(() => new Date().getFullYear())

const activeModelCat = ref('all')

const stats = [
  { value: '99.9%', label: '服务可用性' },
  { value: '<200ms', label: '平均响应' },
  { value: '7×24h', label: '全天候服务' },
  { value: '100+', label: '可用模型' }
]

const features = [
  { icon: 'server' as const, title: '统一网关', desc: '支持 Claude、GPT、Gemini 等主流模型 API，统一接入格式，一个密钥访问所有模型', iconBg: 'bg-gradient-to-br from-primary-500 to-primary-600 shadow-primary-500/30' },
  { icon: 'shield' as const, title: '安全稳定', desc: '企业级安全架构，数据加密传输，独立 API Key 隔离，多节点负载均衡高可用', iconBg: 'bg-gradient-to-br from-amber-500 to-orange-500 shadow-amber-500/30' },
  { icon: 'chart' as const, title: '灵活计费', desc: 'Token 级精确计量，实时余额追踪，透明定价无隐藏费用，用多少付多少', iconBg: 'bg-gradient-to-br from-rose-500 to-pink-500 shadow-rose-500/30' }
]

const modelCategories = [
  { id: 'all', name: '全部' },
  { id: 'anthropic', name: 'Anthropic' },
  { id: 'openai', name: 'OpenAI' },
  { id: 'google', name: 'Google' }
]

const models = [
  { id: 1, name: 'Claude Opus 4', provider: 'Anthropic', abbr: 'C', color: 'bg-gradient-to-br from-orange-500 to-amber-600', context: '200K', category: 'anthropic' },
  { id: 2, name: 'Claude Sonnet 4', provider: 'Anthropic', abbr: 'C', color: 'bg-gradient-to-br from-orange-500 to-amber-600', context: '200K', category: 'anthropic' },
  { id: 3, name: 'Claude Haiku 3.5', provider: 'Anthropic', abbr: 'C', color: 'bg-gradient-to-br from-orange-500 to-amber-600', context: '200K', category: 'anthropic' },
  { id: 4, name: 'GPT-4o', provider: 'OpenAI', abbr: 'G', color: 'bg-gradient-to-br from-emerald-500 to-green-600', context: '128K', category: 'openai' },
  { id: 5, name: 'GPT-4o-mini', provider: 'OpenAI', abbr: 'G', color: 'bg-gradient-to-br from-emerald-500 to-green-600', context: '128K', category: 'openai' },
  { id: 6, name: 'o1-pro', provider: 'OpenAI', abbr: 'O', color: 'bg-gradient-to-br from-emerald-500 to-green-600', context: '200K', category: 'openai' },
  { id: 7, name: 'o3', provider: 'OpenAI', abbr: 'O', color: 'bg-gradient-to-br from-emerald-500 to-green-600', context: '200K', category: 'openai' },
  { id: 8, name: 'o4-mini', provider: 'OpenAI', abbr: 'O', color: 'bg-gradient-to-br from-emerald-500 to-green-600', context: '200K', category: 'openai' },
  { id: 9, name: 'Gemini 2.5 Pro', provider: 'Google', abbr: 'G', color: 'bg-gradient-to-br from-blue-500 to-indigo-600', context: '1M', category: 'google' },
  { id: 10, name: 'Gemini 2.5 Flash', provider: 'Google', abbr: 'G', color: 'bg-gradient-to-br from-blue-500 to-indigo-600', context: '1M', category: 'google' },
  { id: 11, name: 'Gemini 2.0 Flash', provider: 'Google', abbr: 'G', color: 'bg-gradient-to-br from-blue-500 to-indigo-600', context: '1M', category: 'google' },
  { id: 12, name: 'Claude Opus 4.5', provider: 'Anthropic', abbr: 'C', color: 'bg-gradient-to-br from-orange-500 to-amber-600', context: '200K', category: 'anthropic' }
]

const filteredModels = computed(() => {
  if (activeModelCat.value === 'all') return models
  return models.filter(m => m.category === activeModelCat.value)
})

function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

function initTheme() {
  const savedTheme = localStorage.getItem('theme')
  if (
    savedTheme === 'dark' ||
    (!savedTheme && window.matchMedia('(prefers-color-scheme: dark)').matches)
  ) {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }
}

onMounted(() => {
  initTheme()
  authStore.checkAuth()
  if (!appStore.publicSettingsLoaded) {
    appStore.fetchPublicSettings()
  }
})
</script>
