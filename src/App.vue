<script setup>
import { ref, computed, onMounted } from "vue"

const currentView = ref("home")

const tasks = ref([
  { id: 1, title: "Belajar Vue.js 3 Composition API", isDone: true, category: "Belajar" },
  { id: 2, title: "Eksplorasi tren Glassmorphism UI", isDone: false, category: "Desain" },
  { id: 3, title: "Commit code ke GitHub", isDone: false, category: "Coding" }
])

const taskInput = ref("")
const selectedCategory = ref("General")
const searchQuery = ref("")
const currentFilter = ref("all") // 'all', 'active', 'completed'

// Fitur Streak Harian & Hari Terakhir Akses
const streakDays = ref(3)

const categories = ["General", "Belajar", "Desain", "Coding", "Lifestyle"]

const getCategoryColor = (cat) => {
  switch(cat) {
    case 'Belajar': return 'bg-amber-500/20 text-amber-300 border-amber-500/30'
    case 'Desain': return 'bg-pink-500/20 text-pink-300 border-pink-500/30'
    case 'Coding': return 'bg-cyan-500/20 text-cyan-300 border-cyan-500/30'
    case 'Lifestyle': return 'bg-emerald-500/20 text-emerald-300 border-emerald-500/30'
    default: return 'bg-blue-500/20 text-blue-300 border-blue-500/30'
  }
}

const addTask = () => {
  if (taskInput.value.trim() === "") return alert("Task tidak boleh kosong bro! 😭")

  const newTask = {
    id: Date.now(),
    title: taskInput.value,
    isDone: false,
    category: selectedCategory.value
  }

  tasks.value.unshift(newTask)
  taskInput.value = ""
  currentView.value = "home"
}

const removeTask = (id) => {
  const isConfirmed = confirm("Yakin mau hapus task ini?")
  if (!isConfirmed) return

  tasks.value = tasks.value.filter(task => task.id !== id)
}

const clearCompleted = () => {
  tasks.value = tasks.value.filter(task => !task.isDone)
}

const completionPercentage = computed(() => {
  if (tasks.value.length === 0) return 0
  const completed = tasks.value.filter(task => task.isDone).length
  return Math.round((completed / tasks.value.length) * 100)
})

const filteredTasks = computed(() => {
  let result = tasks.value

  if (searchQuery.value.trim() !== "") {
    result = result.filter(task => 
      task.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    )
  }

  if (currentFilter.value === "active") {
    return result.filter(task => !task.isDone)
  } else if (currentFilter.value === "completed") {
    return result.filter(task => task.isDone)
  }
  return result
})

const activeTaskCount = computed(() => {
  return tasks.value.filter(task => !task.isDone).length
})

// Vibe Quotes dinamis ala Gen Z biar seru
const vibeQuote = computed(() => {
  if (activeTaskCount.value === 0) return "Zero tasks left! W moment, saatnya rebahan santai 😎🌴"
  if (completionPercentage.value > 50) return "Udah setengah jalan nih, keep going and slay! 🔥✨"
  return "Locked in! Waktunya gas pol selesein tugas hari ini ⚡"
})
</script>

<template>
  <!-- Background dengan Orbs Neon yang Colorful -->
  <div class="min-h-screen bg-[#07090E] bg-[radial-gradient(circle_at_top_left,rgba(99,102,241,0.25),transparent_50%),radial-gradient(circle_at_bottom_right,rgba(6,182,212,0.2),transparent_50%)] flex items-center justify-center p-4 font-sans selection:bg-cyan-400 selection:text-black">
    
    <!-- Main Container -->
    <main class="w-full max-w-lg bg-[#0E1424]/90 backdrop-blur-2xl border border-white/10 rounded-[32px] shadow-[0_0_60px_rgba(99,102,241,0.15)] p-6 md:p-8 text-white flex flex-col justify-between min-h-[580px] relative overflow-hidden">
      
      <!-- Glow Accent -->
      <div class="absolute -top-24 -right-24 w-48 h-48 bg-cyan-500/20 rounded-full blur-3xl pointer-events-none"></div>
      <div class="absolute -bottom-24 -left-24 w-48 h-48 bg-indigo-500/20 rounded-full blur-3xl pointer-events-none"></div>

      <div class="relative z-10 flex flex-col">
        <!-- Header, Streak Badge & Progress Bar -->
        <div class="mb-5">
          <div class="flex items-center justify-between mb-3">
            <div>
              <h1 class="text-3xl font-black tracking-tight bg-gradient-to-r from-cyan-400 via-indigo-300 to-pink-400 bg-clip-text text-transparent">
                Taskify ⚡
              </h1>
              <p class="text-indigo-200/70 text-xs font-medium mt-0.5">
                {{ vibeQuote }}
              </p>
            </div>
            
            <!-- Streak & Active Badge Container -->
            <div class="flex items-center gap-2">
              <!-- Streak Badge (Fitur Gen Z Favorit) -->
              <div class="bg-amber-500/10 border border-amber-500/30 px-3 py-1.5 rounded-2xl text-xs font-bold text-amber-300 shadow-sm flex items-center gap-1">
                🔥 {{ streakDays }}d
              </div>
              <!-- Badge Total Active -->
              <div class="bg-indigo-500/10 border border-indigo-500/30 px-3 py-1.5 rounded-2xl text-xs font-bold text-indigo-300 shadow-sm flex items-center gap-1.5">
                <span class="w-2 h-2 rounded-full bg-cyan-400 animate-pulse"></span>
                {{ activeTaskCount }} left
              </div>
            </div>
          </div>

          <!-- Dynamic Progress Bar -->
          <div class="bg-white/5 border border-white/10 rounded-2xl p-3 backdrop-blur-md">
            <div class="flex justify-between text-xs font-semibold mb-1.5">
              <span class="text-indigo-200/80">Daily Progress</span>
              <span class="text-cyan-400 font-bold">{{ completionPercentage }}%</span>
            </div>
            <div class="w-full h-2 bg-white/10 rounded-full overflow-hidden p-0.5">
              <div 
                class="h-full bg-gradient-to-r from-cyan-400 via-indigo-500 to-pink-500 rounded-full transition-all duration-500 shadow-[0_0_12px_rgba(6,182,212,0.5)]"
                :style="{ width: completionPercentage + '%' }"
              ></div>
            </div>
          </div>
        </div>

        <!-- VIEW 1: HOME -->
        <div v-if="currentView === 'home'">
          <!-- Search Bar -->
          <div class="relative mb-3.5">
            <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 pointer-events-none text-indigo-400">
              🔍
            </span>
            <input
              v-model="searchQuery"
              type="text"
              placeholder="Search your missions..."
              class="w-full bg-white/5 border border-white/10 rounded-2xl pl-10 pr-4 py-3 text-sm text-white placeholder-indigo-300/30 outline-none focus:border-cyan-400 focus:ring-2 focus:ring-cyan-400/20 transition-all shadow-inner"
            />
          </div>

          <!-- Filter Tabs & Clear Action -->
          <div class="flex flex-col sm:flex-row items-center justify-between gap-3 mb-3.5 text-xs">
            <div class="flex gap-1 bg-white/5 p-1 rounded-2xl border border-white/10 w-full sm:w-auto">
              <button 
                @click="currentFilter = 'all'" 
                :class="currentFilter === 'all' ? 'bg-gradient-to-r from-cyan-500 to-indigo-600 text-white font-bold shadow-md shadow-cyan-500/20' : 'text-indigo-200/60 hover:text-white'"
                class="flex-1 sm:flex-none px-3 py-1.5 rounded-xl transition-all cursor-pointer"
              >
                All
              </button>
              <button 
                @click="currentFilter = 'active'" 
                :class="currentFilter === 'active' ? 'bg-gradient-to-r from-cyan-500 to-indigo-600 text-white font-bold shadow-md shadow-cyan-500/20' : 'text-indigo-200/60 hover:text-white'"
                class="flex-1 sm:flex-none px-3 py-1.5 rounded-xl transition-all cursor-pointer"
              >
                Active
              </button>
              <button 
                @click="currentFilter = 'completed'" 
                :class="currentFilter === 'completed' ? 'bg-gradient-to-r from-cyan-500 to-indigo-600 text-white font-bold shadow-md shadow-cyan-500/20' : 'text-indigo-200/60 hover:text-white'"
                class="flex-1 sm:flex-none px-3 py-1.5 rounded-xl transition-all cursor-pointer"
              >
                Done
              </button>
            </div>

            <button 
              @click="clearCompleted"
              class="text-rose-400/70 hover:text-rose-300 transition-colors cursor-pointer text-xs font-semibold underline underline-offset-4"
            >
              Clear completed
            </button>
          </div>

          <!-- Task List (Tanpa Scrollbar Mengganggu) -->
          <div class="max-h-[220px] overflow-y-auto pr-0 [scrollbar-width:none] [-ms-overflow-style:none] [&::-webkit-scrollbar]:hidden">
            <ul class="space-y-3 pb-1">
              <template v-for="task in filteredTasks" :key="task.id">
                <li class="flex items-center justify-between bg-white/5 border border-white/10 rounded-2xl p-3.5 hover:bg-white/10 hover:border-cyan-400/40 transition-all group shadow-sm">
                  <label class="flex items-center gap-3 cursor-pointer flex-1 mr-2">
                    <input 
                      v-model="task.isDone" 
                      type="checkbox" 
                      class="w-5 h-5 rounded-lg border-white/20 bg-white/5 text-cyan-500 focus:ring-0 focus:ring-offset-0 cursor-pointer accent-cyan-400 transition-all" 
                    />
                    <div class="flex flex-col">
                      <span :class="task.isDone ? 'line-through text-indigo-300/30' : 'text-slate-100 font-medium'" class="transition-colors text-sm">
                        {{ task.title }}
                      </span>
                      <!-- Kategori Badge -->
                      <span :class="getCategoryColor(task.category)" class="text-[10px] font-bold px-2 py-0.5 rounded-md border w-fit mt-1">
                        {{ task.category }}
                      </span>
                    </div>
                  </label>

                  <button
                    @click="removeTask(task.id)"
                    class="text-rose-400/50 hover:text-rose-300 hover:bg-rose-500/10 p-2 rounded-xl transition-all cursor-pointer"
                    title="Delete task"
                  >
                    <span class="text-xs font-bold">Hapus</span>
                  </button>
                </li>
              </template>
              
              <div v-if="filteredTasks.length === 0" class="text-center py-10 text-indigo-200/40 text-sm font-medium">
                Zero tasks here. Time to chill! 😎🌴
              </div>
            </ul>
          </div>
        </div>

        <!-- VIEW 2: ADD TASK -->
        <div v-if="currentView === 'add'" class="py-2">
          <h2 class="text-xl font-bold mb-3 text-cyan-300 tracking-wide">Add New Mission 🚀</h2>
          <form @submit.prevent="addTask" class="flex flex-col gap-4">
            <div>
              <label class="text-xs font-semibold text-indigo-200/70 mb-1.5 block">Task Title</label>
              <input
                v-model="taskInput"
                type="text"
                placeholder="What's the main quest today?"
                class="w-full bg-white/5 border border-white/10 rounded-2xl px-4 py-3.5 text-sm text-white placeholder-indigo-300/30 outline-none focus:border-cyan-400 focus:ring-2 focus:ring-cyan-400/20 transition-all shadow-inner"
              />
            </div>

            <div>
              <label class="text-xs font-semibold text-indigo-200/70 mb-1.5 block">Category</label>
              <div class="flex flex-wrap gap-2">
                <button
                  v-for="cat in categories"
                  :key="cat"
                  type="button"
                  @click="selectedCategory = cat"
                  :class="selectedCategory === cat ? 'bg-cyan-500 text-black font-bold shadow-md shadow-cyan-500/30 border-transparent' : 'bg-white/5 text-indigo-200/70 border-white/10 hover:bg-white/10'"
                  class="text-xs px-3.5 py-2 rounded-xl border transition-all cursor-pointer"
                >
                  {{ cat }}
                </button>
              </div>
            </div>

            <button
              type="submit"
              class="bg-gradient-to-r from-cyan-400 via-indigo-500 to-pink-500 hover:opacity-90 active:scale-95 text-black font-extrabold text-sm px-5 py-3.5 rounded-2xl shadow-lg shadow-cyan-500/25 transition-all duration-200 cursor-pointer mt-2"
            >
              Lock In Task ⚡
            </button>
          </form>
        </div>

        <!-- VIEW 3: STATS & STREAK INFO -->
        <div v-if="currentView === 'stats'" class="py-3 text-center">
          <h2 class="text-xl font-bold mb-1 text-cyan-300">Vibe Check / Stats 📊</h2>
          <p class="text-xs text-indigo-200/70 mb-5">Consistency is key! Keep your streak alive ✨</p>
          
          <div class="grid grid-cols-2 gap-3 mb-4">
            <div class="bg-white/5 border border-white/10 p-4 rounded-2xl text-center">
              <span class="text-2xl font-black text-amber-400">{{ streakDays }} Days</span>
              <p class="text-[11px] text-indigo-200/60 mt-1 uppercase font-semibold">Active Streak 🔥</p>
            </div>
            <div class="bg-white/5 border border-white/10 p-4 rounded-2xl text-center">
              <span class="text-2xl font-black text-pink-400">{{ activeTaskCount }}</span>
              <p class="text-[11px] text-indigo-200/60 mt-1 uppercase font-semibold">Remaining</p>
            </div>
          </div>

          <div class="bg-gradient-to-br from-cyan-500/10 via-indigo-500/10 to-pink-500/10 border border-white/10 p-4 rounded-3xl shadow-inner inline-block w-full">
            <span class="text-3xl font-black bg-gradient-to-r from-cyan-400 to-pink-400 bg-clip-text text-transparent">
              {{ completionPercentage }}%
            </span>
            <p class="text-xs font-bold text-indigo-200 mt-1 tracking-wider uppercase">Productivity Score</p>
          </div>
        </div>
      </div>

      <!-- BOTTOM NAVIGATION BAR -->
      <nav class="flex items-center justify-around border-t border-white/10 pt-4 mt-5 bg-[#0E1424]/60 relative z-10">
        <button 
          @click="currentView = 'home'"
          :class="currentView === 'home' ? 'text-cyan-400 font-bold scale-105' : 'text-indigo-200/50 hover:text-white'"
          class="flex flex-col items-center gap-1 transition-all cursor-pointer text-xs"
        >
          <span class="text-xl">🏠</span>
          Home
        </button>

        <!-- Floating Neon Action Button -->
        <button 
          @click="currentView = 'add'"
          :class="currentView === 'add' ? 'bg-gradient-to-r from-cyan-400 to-pink-400 text-black shadow-[0_0_20px_rgba(6,182,212,0.5)]' : 'bg-gradient-to-r from-cyan-500 to-indigo-600 text-white hover:opacity-90 shadow-lg shadow-cyan-500/30'"
          class="w-13 h-13 rounded-2xl flex items-center justify-center transition-all cursor-pointer -mt-6 border-2 border-[#0E1424] text-2xl font-bold rotate-0 hover:rotate-90 duration-300"
          title="Add Task"
        >
          ✨
        </button>

        <button 
          @click="currentView = 'stats'"
          :class="currentView === 'stats' ? 'text-cyan-400 font-bold scale-105' : 'text-indigo-200/50 hover:text-white'"
          class="flex flex-col items-center gap-1 transition-all cursor-pointer text-xs"
        >
          <span class="text-xl">📊</span>
          Stats
        </button>
      </nav>

    </main>
  </div>
</template>