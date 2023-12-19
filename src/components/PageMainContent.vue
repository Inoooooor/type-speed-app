<template>
  <div class="w-full basis-2/3">
    <div class="container h-full">
      <div class="flex flex-col justify-evenly h-full">
        <div
          class="fetchedtext bg-white rounded-3xl basis-1/4 sm:text-2xl text-base p-5 shadow-xl"
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
            class="placeholder:text-2xl w-full h-full focus:outline-none sm:text-2xl text-base resize-none"
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
</template>

<script setup>
import { ref, computed } from "vue"
import axios from "axios"

const templateArray = ref([])
const fetchedText = ref("")
const inputValue = ref("")

const speed = ref(0)
const time = ref(59)

let timer = null

const inputValueArray = computed(() => inputValue.value.split(""))
const charsTypedLength = computed(() => inputValue.value.length)
const fetchedTextLength = computed(() => fetchedText.value.length)

const fetchText = async () => {
  const url = "https://api.api-ninjas.com/v1/quotes?category=happiness"

  const { data } = await axios.get(url, {
    headers: {
      "Content-Type": "multipart/form-data",
      "x-api-key": "4H8I64Fd/e5raYcO860ePw==MDI0QwiC0pKXicg7",
    },
  })

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

const checkInput = () => {
  measureTypeSpeed()
  handleTimer()

  // console.log(inputValueArray.value)
  inputValueArray.value.forEach((letter, index) => {
    if (letter !== templateArray.value[index].innerText) {
      templateArray.value[index].className = "text-red-600 underline"
    } else {
      templateArray.value[index].className = "text-green-600"
    }
  })
}

const measureTypeSpeed = () => {
  const timeGiven = 60
  const timeElapsed = timeGiven - time.value
  const charsPerMinute = Math.round((charsTypedLength.value / timeElapsed) * 60)

  speed.value = charsPerMinute
}

fetchText()
</script>

<style scoped></style>
