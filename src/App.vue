<template>
  <header>
    
  </header>
  <main class="main">
    <h1 class="main__header">Losowanie Pytań</h1>
    <p ref="$questionElement" id="question" class="main__question">{{ $randomQuestion }}</p>
    <button @click="getRandomQuestion()" class="main__btn-random-question">Wygeneruj losowe pytanie</button>

  </main>
</template>


<script setup>
import { ref } from 'vue';

let $randomQuestion = ref('Którego gatunku został zaobserwowany największe zwierze w historii? - płetwala błękitnego');
let $questions = ref([]);

async function loadQuestions() {
  try {
    const response = await fetch('questions.txt')
    const data = await response.text()
    return data.split('\n').filter(line => line.trim() !== '')
  } catch (error) {
    console.error('Błąd podczas wczytywania pytań', error)
    return []
  }
}

async function createQuestionsArray () {
  $questions.value = await loadQuestions()
}

async function getRandomQuestion() {
  createQuestionsArray()
  $questions.value = $questions.value.filter((question) => question !== $randomQuestion.value)
  const randomIndex = Math.floor(Math.random() * $questions.value.length)
  console.log($questions.value);
  $randomQuestion.value = $questions.value[randomIndex]
}

createQuestionsArray()
</script>


<style>
  
  .main__header::before {
    content: var(--headerText);
    position: absolute;
    bottom: 0;
    font-size: 6rem;
    
  }

</style> 
