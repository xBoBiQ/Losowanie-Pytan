<template>
  <header>
    
  </header>
  <main class="main">
    <h1 class="main__header">Losowanie Pytań</h1>
    <p ref="$questionElement" id="question" class="main__question">{{ $randomQuestion }}</p>
    <button @click="getRandomQuestion()" class="main__btn-random-question">Wygeneruj losowe pytanie</button>

    <h3 >Wklej liste</h3>
    <textarea class="main__textarea" id="textarea" cols="30" rows="10"></textarea>
    <button class="main__load-questions-btn" @click="createQuestionsArray()">Załaduj Pytania</button>
  </main>
</template>


<script setup>
import { ref } from 'vue';

let $randomQuestion = ref('Którego gatunku został zaobserwowany największe zwierze w historii? - płetwala błękitnego');
let $questions = ref([]);

async function loadQuestions() {
  try {
    const textarea = document.getElementById('textarea');
    const data = textarea.value;
    return data.split('\n').filter(line => line.trim() !== '');
  } catch (error) {
    console.error('Błąd podczas wczytywania pytań', error);
    return [];
  }
}

async function createQuestionsArray () {
  $questions.value = await loadQuestions()
  const textarea = document.getElementById('textarea');
  textarea.value = ''
}

async function getRandomQuestion() {
  createQuestionsArray()
  $questions.value = $questions.value.filter((question) => question !== $randomQuestion.value)
  const randomIndex = Math.floor(Math.random() * $questions.value.length)
  console.log($questions.value);
  $randomQuestion.value = $questions.value[randomIndex]
}

</script>
