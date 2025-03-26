<script setup>
import { ref, useTemplateRef, onMounted, watch } from "vue";
import useLocalStorage from "./composables/useLocalStorage.js";

let savedLists = useLocalStorage([], "savedLists");
let questionsMainArray = useLocalStorage([], "questionsMainArr");
let randomQuestion = useLocalStorage(
	"Którego gatunku został zaobserwowany największe zwierze w historii? - płetwala błękitnego",
	"randomQuestion"
);
let questions = useLocalStorage([], "questionsCopyArr");
let isUserWriting = useLocalStorage(true, "isUserWriting");
const textareaText = useLocalStorage("", "textareaText");
const saveLessonTitle = useLocalStorage("", "saveLessonTitle");
const listTitle = useLocalStorage("", "listTitle");

const isSavedListsModalOpen = useLocalStorage(false, "isSavedListsModalOpen");
const isSaveListModalOpen = useLocalStorage(false, "isSaveListModalOpen");
const modalError = useLocalStorage(false, "modalError");

function filterQuestions() {
	// console.log(textareaText.value);
	const data = textareaText.value;
	return data.split("\n").filter((line) => line.trim() !== "");
}

function getRandomQuestion() {
	if (questions.value.length === 0) {
		randomQuestion.value = "Brak pytań do losowania";
	} else {
		const randomIndex = Math.floor(Math.random() * questions.value.length);
		randomQuestion.value = questions.value[randomIndex];
		questions.value.splice(randomIndex, 1);
	}
}

function createQuestionsArray() {
	questionsMainArray.value = filterQuestions();

	// checks if the array is empty
	if (questionsMainArray.value.length !== 0) {
		questions.value = [...questionsMainArray.value];
		// textareaText.value = "";
		isUserWriting.value = false;
		getRandomQuestion();
	}
}

function repeatQuestionsSession() {
	questions.value = [...questionsMainArray.value];
	getRandomQuestion();
}

function deleteQuestionsArrays() {
	questionsMainArray.value = [];
	questions.value = [];
	textareaText.value = "";
	isUserWriting.value = true;
	randomQuestion.value = "Brak pytań do losowania";
}

function saveList() {
	if (saveLessonTitle.value === "") {
		modalError.value = true;
	} else {
		savedLists.value.push({
			title: saveLessonTitle.value,
			questions: [...questionsMainArray.value],
		});
		isSaveListModalOpen.value = false;
		saveLessonTitle.value = "";
		modalError.value = false;
	}
}

function loadQuestionsFromList(id) {
	questionsMainArray.value = savedLists.value[id].questions;
	questions.value = [...questionsMainArray.value];
	listTitle.value = savedLists.value[id].title;
	isUserWriting.value = false;
	isSavedListsModalOpen.value = false;
	getRandomQuestion();
}

function removeSavedList(id) {
	savedLists.value.splice(id, 1);
}

function closeModal() {
	isSaveListModalOpen.value = false;
	isSavedListsModalOpen.value = false;
	modalError.value = false;
}


</script>

<template>
	<main class="main">
		<h1 class="header">Losowanie Pytań</h1>

		<div class="box">
			<div v-if="isUserWriting === true" class="box__user-writes box__content">
				<h3 class="box__header">Wklej liste</h3>
				<textarea
				
					v-model="textareaText"
					class="box__textarea"
					id="textarea"
				></textarea>

				<div class="box__buttons">
					<button
						@click="createQuestionsArray()"
						class="btn btn--inverted btn--bold"
					>
						Załaduj Pytania
					</button>
					<button @click="isUserWriting = false;" class="btn btn--bold">></button>
					<button @click="deleteQuestionsArrays()" class="btn">🗑</button>
					<button @click="isSavedListsModalOpen = true" class="btn">
						Zapisane Pytania
					</button>
				</div>
			</div>

			<div v-if="isUserWriting === false" class="box__user-draws box__content">
				<p id="question" class="question">
					{{ randomQuestion }}
				</p>
				<button @click="getRandomQuestion()" class="btn">
					Wygeneruj losowe pytanie
				</button>

				<ol class="box__current-list">
					<li v-for="(item, index) in questionsMainArray" :key="index">
						{{ item }}
					</li>
				</ol>
				<div class="box__buttons">
					<button @click="isUserWriting = true;" class="btn btn--bold"><</button>
					<button @click="repeatQuestionsSession()" class="btn btn--bold">
						↻
					</button>
					<button @click="deleteQuestionsArrays()" class="btn">🗑</button>
					<button @click="isSaveListModalOpen = true" class="btn">
						Zapisz
					</button>
				</div>
			</div>

			<div v-if="isSaveListModalOpen" class="modal">
				<div @click="closeModal()" class="modal__shadow"></div>
				<div class="modal__content">
					<h5 class="modal__heading">Zapisz Liste</h5>
					<!-- <button>✕</button> -->
					<label for="title">Tytuł</label>
					<input v-model="saveLessonTitle" type="text" name="title" />
					<p
						:class="modalError ? 'modal__error--active' : ''"
						class="modal__error"
					>
						Tytuł nie może być pusty
					</p>
					<button @click="saveList()" class="btn">Zapisz</button>
				</div>
			</div>

			<div v-if="isSavedListsModalOpen" class="modal">
				<div @click="closeModal()" class="modal__shadow"></div>
				<div class="modal__content">
					<h5 class="modal__heading">Wczytaj Zapisaną Liste</h5>

					<ul class="modal__list">
						<li
							v-for="(item, index) in savedLists"
							:key="index"
							class="modal__list-item"
						>
							<p @click="loadQuestionsFromList(index)" class="modal__title">
								{{ item.title }}
							</p>
							<button
								@click="removeSavedList(index)"
								class="modal__remove-list-item"
							>
								✕
							</button>
						</li>
					</ul>
				</div>
			</div>
		</div>
	</main>
</template>
