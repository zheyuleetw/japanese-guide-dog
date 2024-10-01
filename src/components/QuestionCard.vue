<script setup>
import { ref, onMounted, computed } from 'vue'
import questions from '/src/assets/questions/date.json'

const currentQuestion = ref(null)
const wrongBacket = ref([])
const correctBacket = ref([])
const answeredBacket = ref([])
const pickedKey = ref(null)
const answerKey = ref(null)
const questionIndex = ref(0)
const pickQuestion = () => {
    clean()
    questionIndex.value = randomPick()
    const answered = [...wrongBacket.value, ...correctBacket.value]
    currentQuestion.value = {
        question: questions[questionIndex.value].question,
        options: questions[questionIndex.value].options,
        correctAnswer: questions[questionIndex.value].correct_answer,
        index: questionIndex.value
    }
}

const randomPick = () => {
    let randomIndex;
    do {
        randomIndex = Math.floor(Math.random() * questions.length);
    } while (answeredBacket.value.includes(randomIndex));
    return randomIndex;
}
const clean = () => {
    pickedKey.value = null
    answerKey.value = null
    if (answeredBacket.length === questions.length) {
        wrongBacket.value = []
        correctBacket.value = []
        answeredBacket.value = []
    }
}
const nextDisabled = computed(() => {
    return pickedKey.value === null
})
onMounted(pickQuestion)
const toAnswer = (picked) => {

    if (pickedKey.value !== null) return

    answerKey.value = currentQuestion.value.correctAnswer
    pickedKey.value = picked
    if (picked === currentQuestion.value.correctAnswer) {
        correctBacket.value.push(currentQuestion.index)
    } else {
        wrongBacket.value.push(currentQuestion.index)
    }
    answeredBacket.value.push(currentQuestion.index)
}
</script>

<template>
    <div class="title">
        <p v-if="currentQuestion">{{ currentQuestion.question }}</p>
        <p v-else>正在加載問題...</p>
    </div>
    <div class="result">
        <p>✅ {{ correctBacket.length }} ❌ {{ wrongBacket.length }} </p>
    </div>
    <div class="options" v-if="currentQuestion">
        <button v-for="(value, key) in currentQuestion.options" @click="toAnswer(key)" :key="key"
            :class="{ correct: answerKey === key, wrong: pickedKey === key && answerKey !== key }">{{ value }}</button>
    </div>
    <div class="next">
        <button @click="pickQuestion" :disabled="nextDisabled">次へ</button>
    </div>
</template>

<style>
.correct {
    background-color: greenyellow;
}

.wrong {
    background-color: rgb(255, 33, 17);
}

.title {
    height: 20vh;
}

.title p {
    font-size: clamp(5rem, 2vw, 2.5rem);
}

.result {
    margin: 20px;
    height: 2vh;
}

.options {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
    height: 20vh;
}

.next {
    margin: 20px;
    height: 5vh;
}
</style>