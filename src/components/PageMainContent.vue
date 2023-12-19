<template>
  <div class="w-full basis-2/3">
    <div class="container h-full">
      <div class="flex flex-col justify-evenly h-full">
        <div
          class="bg-white rounded-3xl basis-1/4 sm:text-2xl text-base p-5 shadow-xl select-none"
        >
          <span
            class="text-letter"
            v-for="(letter, index) in fetchedText"
            :key="index"
            ref="templateArray"
            >{{ letter }}</span
          >
        </div>
        <div class="input bg-white rounded-3xl basis-1/2 p-5 flex shadow-xl">
          <textarea
            v-model="inputValue"
            :maxlength="fetchedTextLength"
            :placeholder="fetchedText"
            class="sm:placeholder:text-2xl placeholder:text-base w-full h-full focus:outline-none text-base sm:text-2xl resize-none"
            @input="checkInput"
          ></textarea>
          <div
            class="basis-1/5 flex flex-col justify-evenly border-l-2 p-2 text-center"
          >
            <div class="flex basis-1/2 justify-center items-center border-b-2">
              <p>Скорость печати: {{ speed }} знаков/сек</p>
            </div>
            <div class="flex basis-1/2 justify-center items-center">
              <p>Оставшееся время: {{ time }}с</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <el-dialog
    v-model="isDialogVisible"
    align-center
    width="70%"
    class="text-2xl"
  >
    <template #header> Результаты </template>

    <p class="text-lg">Времени потрачено: {{ timeElapsed }}</p>
    <p class="text-lg">Совершено ошибок: {{ errorsCount }}</p>
    <p class="text-lg">Скорость печати: {{ speed }} знаков/сек</p>
    <template #footer>
      <el-button
        @click="refreshPage"
        type="primary"
        >Попробовать снова</el-button
      >
    </template>
  </el-dialog>
</template>

<script setup>
import { ref, computed, watch } from "vue"
import axios from "axios"

const templateArray = ref([])
const fetchedText = ref("")
const inputValue = ref("")

const speed = ref(0)
const time = ref(59)
const errorsCount = ref(0)
const isDialogVisible = ref(false)

let timer = null

const inputValueArray = computed(() => inputValue.value.split(""))
const charsTypedLength = computed(() => inputValue.value.length)
const fetchedTextLength = computed(() => fetchedText.value.length)
const timeElapsed = computed(() => 60 - time.value)

const fetchText = async () => {
  const url = new URL("https://api.api-ninjas.com/v1/quotes")
  url.searchParams.set("category", "happiness")

  const options = {
    headers: {
      "x-api-key": "4H8I64Fd/e5raYcO860ePw==MDI0QwiC0pKXicg7",
    },
  }

  const { data } = await axios.get(url, options)

  fetchedText.value = data[0].quote
}

const handleTimer = () => {
  if (!timer) {
    timer = setInterval(() => {
      if (time.value === 1) clearInterval(timer)
      time.value--
    }, 1000)
  }
}

const measureTypeSpeed = () => {
  const timeGiven = 60
  const timeElapsed = timeGiven - time.value
  const charsPerMinute = Math.round((charsTypedLength.value / timeElapsed) * 60)

  speed.value = charsPerMinute
}

const checkInput = () => {
  measureTypeSpeed()
  handleTimer()

  inputValueArray.value.forEach((letter, index) => {
    if (letter !== templateArray.value[index].innerText) {
      templateArray.value[index].className = "text-red-600 underline"
    } else {
      templateArray.value[index].className = "text-green-600"
    }
  })
}

const countErrors = () => {
  errorsCount.value = templateArray.value.reduce((acc, el) => {
    if (el.className !== "text-red-600 underline") return acc
    return acc + 1
  }, 0)
}

fetchText()

watch(inputValue, (newValue) => {
  if (newValue.length === fetchedTextLength.value) {
    clearInterval(timer)
    countErrors()
    isDialogVisible.value = true
  }
})

watch(time, (newValue) => {
  if (newValue === 0) {
    countErrors()
    isDialogVisible.value = true
  }
})

const refreshPage = () => location.reload()
</script>

<style scoped></style>
