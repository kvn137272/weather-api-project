 <script setup>
import axios from 'axios'
import { useDateFormat, useNow, useDark, useToggle } from '@vueuse/core'
import { reactive, ref } from 'vue'

const isDark = useDark()
const toggleDark = useToggle(isDark)

const apiKey = 'JLJ3DA7LSM4DN5CFRQUQJTW4T'

const formatted = useDateFormat(useNow(), 'YYYY-MMMM-DD HH:mm:ss')
const weather = reactive({
  data: null,
  status: null,
  showError: false,
  errorMessage: null,
  imgIcon: null
})

// INPUT SEARCH VALUE
const inputValue = ref('')

//FETCH WEATHER FUNCTION
const fetchWeather = async (location = 'iran') => {
  try {
    const { status, data } = await axios.get(
      `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${location}/?key=${apiKey}`
    )
    weather.data = data
    weather.status = status
    weather.imgIcon = data.currentConditions.icon
    weather.showError = false
  } catch ({ response }) {
    weather.errorMessage = response.data
    weather.showError = true
  }
}
fetchWeather()
</script>

<template>
  <div
    class="flex-col h-screen place-content-center place-items-center bg-slate-200"
    v-if="weather.status === 200"
  >
    <main class="flex justify-center">
      <div>
        <div>
          <label>
            <input type="checkbox" checked="checked" class="checkbox" @change="toggleDark()" />
            <span class="ml-2">{{ isDark ? 'dark' : 'light' }}</span>
          </label>
        </div>
        <div class="flex flex-col bg-white rounded p-4 w-96" :class="{ 'bg-black': isDark }">
          <div class="font-bold text-xl text-center capitalize" :class="{ 'text-white': isDark }">
            {{ weather.data.address }}
          </div>
          <div class="text-sm text-gray-500 text-center" :class="{ 'text-white': isDark }">
            {{ formatted }}
          </div>
          <div
            class="mt-6 text-6xl self-center inline-flex items-center justify-center rounded-lg text-indigo-400 h-24 w-24"
          >
            <img :src="`./icons/${weather.imgIcon}.png`" />
          </div>
          <div class="flex flex-row items-center justify-center mt-6">
            <div class="font-medium text-6xl" :class="{ 'text-white': isDark }">
              {{ weather.data.currentConditions.temp }}°
            </div>
            <div class="flex flex-col items-center ml-6">
              <div class="text-center" :class="{ 'text-white': isDark }">
                {{ weather.data.currentConditions.conditions }}
              </div>
              <div class="mt-1">
                <span class="text-sm"><i class="far fa-long-arrow-up"></i></span>
                <span class="text-sm font-light text-gray-500" :class="{ 'text-white': isDark }"
                  >{{ weather.data.days[0].tempmax }}°C</span
                >
              </div>
              <div>
                <span class="text-sm"><i class="far fa-long-arrow-down"></i></span>
                <span class="text-sm font-light text-gray-500" :class="{ 'text-white': isDark }"
                  >{{ weather.data.days[0].tempmin }}°C</span
                >
              </div>
            </div>
          </div>
          <div class="flex flex-row justify-between mt-6">
            <div class="flex flex-col items-center">
              <div class="font-medium text-sm" :class="{ 'text-white': isDark }">Wind</div>
              <div class="text-sm text-gray-500" :class="{ 'text-white': isDark }">
                {{ weather.data.currentConditions.windspeed }}k/h
              </div>
            </div>
            <div class="flex flex-col items-center">
              <div class="font-medium text-sm" :class="{ 'text-white': isDark }">Humidity</div>
              <div class="text-sm text-gray-500" :class="{ 'text-white': isDark }">
                {{ weather.data.currentConditions.humidity }}%
              </div>
            </div>
            <div class="flex flex-col items-center">
              <div class="font-medium text-sm" :class="{ 'text-white': isDark }">Visibility</div>
              <div class="text-sm text-gray-500" :class="{ 'text-white': isDark }">
                {{ weather.data.currentConditions.visibility }}km
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <div class="w-96 mx-auto">
      <form @submit.prevent="fetchWeather(inputValue)">
        <label
          for="default-search"
          class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-white"
          >Search</label
        >
        <div class="relative">
          <div class="absolute inset-y-0 start-0 flex items-center ps-3 pointer-events-none">
            <svg
              class="w-4 h-4 text-gray-500 dark:text-gray-400"
              aria-hidden="true"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 20 20"
            >
              <path
                stroke="currentColor"
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"
              />
            </svg>
          </div>
          <input
            id="default-search"
            class="block w-full p-4 ps-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            :class="{ 'bg-black': isDark }"
            placeholder="Search Location"
            required
            v-model.trim="inputValue"
            pattern="[a-zA-Z]* | .{0}|.{5,10}"
            maxlength="15"
          />
          <button
            type="submit"
            class="text-white absolute end-2.5 bottom-2.5 bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
            :class="{ 'bg-black border-white border-[1px]': isDark }"
          >
            Search
          </button>
        </div>

        <p class="text-red-500" v-if="weather.showError">{{ weather.errorMessage }}</p>
      </form>
    </div>
  </div>
</template>