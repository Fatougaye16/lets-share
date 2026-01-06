<template>
  <aside id="default-sidebar"
    :class="['fixed top-0 left-0 z-40 h-screen transition-all duration-200', isOpen ? '' : '-translate-x-full sm:-translate-x-full', collapsed ? 'w-20' : 'w-64']"
    aria-label="Sidebar" role="navigation">
    <div class="h-full px-3 py-6 overflow-y-auto bg-amber-950 relative">
      <!-- mobile toggle -->
      <button
        class="sm:hidden absolute top-4 right-4 text-white p-2 rounded-md"
        :aria-expanded="isOpen"
        aria-controls="default-sidebar"
        @click="toggle">
        <span v-if="!isOpen">Open menu</span>
        <span v-else>Close menu</span>
      </button>
      <!-- collapse toggle (desktop) - moved into header below -->
      <ul class="space-y-2 font-medium">
          <div class="flex items-center justify-between gap-4">
            <div class="flex items-center gap-4">
              <!-- always show initials badge; size changes with collapsed state -->
              <div>
                <div
                  role="img"
                  :aria-label="`User initial ${initial}`"
                  :class="collapsed ? 'profile-initials w-10 h-10 flex items-center justify-center rounded-full bg-stone-200 text-amber-950 font-semibold' : 'profile-initials-lg w-12 h-12 flex items-center justify-center rounded-full bg-stone-200 text-amber-950 font-semibold'">
                  {{ initial }}
                </div>
              </div>

              <div v-show="!collapsed" class="font-medium text-white truncate">
                <div>{{ user?.userName }}</div>
                <div class="text-sm text-white dark:text-gray-400 truncate">{{ user?.email }}</div>
              </div>
            </div>
            <div>
              <!-- collapse button when expanded -->
              <button v-show="!collapsed" @click="toggleCollapse" :aria-pressed="collapsed" class="inline-flex sm:inline-flex text-white p-2 rounded-md focus:outline-none focus:ring-2 focus:ring-white" :title="'Collapse sidebar'" aria-label="Collapse sidebar">
                <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" aria-hidden="true">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M15 19l-7-7 7-7" />
                </svg>
              </button>

              <!-- expand button when collapsed (always visible in collapsed state) -->
              <button v-show="collapsed" @click="toggleCollapse" :aria-pressed="collapsed" class="inline-flex text-white p-2 rounded-md focus:outline-none focus:ring-2 focus:ring-white" :title="'Expand sidebar'" aria-label="Expand sidebar">
                <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" aria-hidden="true">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
                </svg>
              </button>
            </div>
          </div>

        <li>
          <RouterLink :class="linkClass('/dashboard')" to="/dashboard" aria-label="Dashboard" :title="collapsed ? 'Dashboard' : null">
            <svg
              class="w-5 h-5  transition duration-75"
              aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 22 21">
              <path
                d="M16.975 11H10V4.025a1 1 0 0 0-1.066-.998 8.5 8.5 0 1 0 9.039 9.039.999.999 0 0 0-1-1.066h.002Z" />
              <path
                d="M12.5 0c-.157 0-.311.01-.565.027A1 1 0 0 0 11 1.02V10h8.975a1 1 0 0 0 1-.935c.013-.188.028-.374.028-.565A8.51 8.51 0 0 0 12.5 0Z" />
            </svg>
            <span v-show="!collapsed" class="ms-3">Dashboard</span>
          </RouterLink>
        </li>
        <li>
          <RouterLink :class="linkClass('/my-documents')" to="/my-documents" aria-label="My Documents" :title="collapsed ? 'My Documents' : null">
            <svg
              class="flex-shrink-0 w-5 h-5 transition duration-75"
              aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 18 18">
              <path
                d="M6.143 0H1.857A1.857 1.857 0 0 0 0 1.857v4.286C0 7.169.831 8 1.857 8h4.286A1.857 1.857 0 0 0 8 6.143V1.857A1.857 1.857 0 0 0 6.143 0Zm10 0h-4.286A1.857 1.857 0 0 0 10 1.857v4.286C10 7.169 10.831 8 11.857 8h4.286A1.857 1.857 0 0 0 18 6.143V1.857A1.857 1.857 0 0 0 16.143 0Zm-10 10H1.857A1.857 1.857 0 0 0 0 11.857v4.286C0 17.169.831 18 1.857 18h4.286A1.857 1.857 0 0 0 8 16.143v-4.286A1.857 1.857 0 0 0 6.143 10Zm10 0h-4.286A1.857 1.857 0 0 0 10 11.857v4.286c0 1.026.831 1.857 1.857 1.857h4.286A1.857 1.857 0 0 0 18 16.143v-4.286A1.857 1.857 0 0 0 16.143 10Z" />
            </svg>
            <span v-show="!collapsed" class="flex-1 ms-3 whitespace-nowrap">My Documents</span>
          </RouterLink>
        </li>
        <li>
          <RouterLink :class="linkClass('/log-out')" to="/log-out" aria-label="Log Out" :title="collapsed ? 'Log Out' : null">
            <svg
              class="flex-shrink-0 w-5 h-5  transition duration-75"
              aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 18 16">
              <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M1 8h11m0 0L8 4m4 4-4 4m4-11h3a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2h-3" />
            </svg>
            <span v-show="!collapsed" class="flex-1 ms-3 whitespace-nowrap">Log Out</span>
          </RouterLink>
        </li>
      </ul>
    </div>
  </aside>
</template>


<script setup>
 import { useUserStore } from '../stores/user'
 import { computed , onMounted, ref, watch } from 'vue'
 import { useRoute } from 'vue-router'
 
const userStore = useUserStore()
 
const user = computed(() => userStore.user)
const initial = computed(() => {
  try {
    return (user.value?.userName && user.value.userName.charAt(0).toUpperCase()) || 'U'
  } catch (e) { return 'U' }
})
const isOpen = ref(window?.innerWidth >= 640)
const collapsed = ref(false)
const route = useRoute()

const toggle = () => { isOpen.value = !isOpen.value }
const toggleCollapse = () => { collapsed.value = !collapsed.value }

const linkClass = (path) => {
  const base = 'flex items-center p-2 rounded-lg text-white group'
  const active = route.path === path || route.path.startsWith(path)
  return `${base} ${active ? 'active-link' : ''}`
}

onMounted(() => {
  // keep sidebar open on wide screens
  const onResize = () => { isOpen.value = window.innerWidth >= 640 }
  window.addEventListener('resize', onResize)

  // restore collapsed state
  try {
    const stored = localStorage.getItem('sidebarCollapsed')
    if (stored !== null) collapsed.value = stored === 'true'
  } catch (e) { /* ignore */ }
})

// persist collapsed state
watch(collapsed, (val) => {
  try { localStorage.setItem('sidebarCollapsed', val ? 'true' : 'false') } catch (e) {}
})

const updateSidebarCssVar = () => {
  const left = !isOpen.value ? '0px' : (collapsed.value ? '80px' : '256px')
  try { document.documentElement.style.setProperty('--sidebar-left', left) } catch (e) {}
}

// update when relevant state changes
watch(collapsed, updateSidebarCssVar)
watch(isOpen, updateSidebarCssVar)
onMounted(updateSidebarCssVar)
</script>

<style scoped>
.active-link {
  background: rgba(255,255,255,0.14) !important;
  color: #fff !important;
  box-shadow: inset 4px 0 0 rgba(250,204,21,0.95);
  font-weight: 600;
}

.active-link svg { color: rgba(250,204,21,1); }

/* ensure collapsed sidebar icons are centered */
.w-20 .group { justify-content: center; }

.profile-initials {
  font-size: 0.95rem;
  background: #ffffff;
  color: #b45309;
  border: 2px solid rgba(255,255,255,0.06);
  box-shadow: 0 4px 10px rgba(0,0,0,0.15);
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.profile-initials-lg {
  font-size: 1rem;
  width: 48px;
  height: 48px;
}

/* ensure name/email stack truncates nicely */
.font-medium.truncate { max-width: 10rem; }
</style>