<template>
	<div class="component-box-ui">
		<b-jumbotron>
			<template slot="lead">
				{{ currentQuestion.question }}
			</template>

			<hr class="my-4" />
			<b-list-group>
				<b-list-group-item 
					v-for="(answer, index) in answers" :key="index" 
					@click="selectAnswer(index)" 
					:class="answerClass(index)"> 
					{{ answer }}
				</b-list-group-item>
			</b-list-group>

			<b-button :disabled="selectedIndex === null || answered" variant="primary" @click="submitAnswer">Submit</b-button>
			<b-button @click="next" variant="success" href="#">Next</b-button>
		</b-jumbotron>
	</div>
</template>
<style scoped>
.list-group {
	margin-bottom: 50px;
}
.btn {
	margin: 0 5px;
}
.list-group-item:hover {
	background-color: #eee;
	cursor: pointer;
}
.selected {
	background-color: lightblue;
}
.correct {
	background-color: lightgreen;
}
.incorrect {
	background-color: red;
}
</style>
<script>
import _ from "lodash";
export default {
	props: {
		currentQuestion: Object,
		next: Function,
		increment: Function,
	},
	data() {
		return {
			selectedIndex: null,
			correctIndex: null,
			shuffledAnswers: [],
			answered: false
		}
	},
	mounted() {
		// this.shuffleAnswers()
	},
	methods: {
		answerClass(index){
			return !this.answered && this.selectedIndex === index ? 'selected' : 
					this.answered && this.correctIndex === index?'correct':
					this.answered && this.selectedIndex == index?'incorrect':'';
		},
		selectAnswer(index) {
			this.selectedIndex = index;
			console.log(index);
		},
		shuffleAnswers() {
			let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
			this.shuffledAnswers = _.shuffle(answers);
			this.correctIndex = _.indexOf(this.shuffledAnswers,this.currentQuestion.correct_answer)
		},
		submitAnswer() {
			let isCorrect = false;
			if (this.selectedIndex === this.correctIndex) {
				isCorrect = true;
			}
			this.answered = true;
			this.increment(isCorrect);
		},
	},
	watch: {
		currentQuestion: {
			immediate: true,
			handler() {
				this.selectedIndex = null; 
				this.shuffleAnswers();
				this.answered = false;
			},
		},
		// currentQuestion(){
		// 	this.selectedIndex = null
		// 	this.shuffleAnswers()
		// }
	},
	computed: {
		answers() {
			let answers = [...this.currentQuestion.incorrect_answers];
			answers.push(this.currentQuestion.correct_answer);
			return answers;
		},
	},
};
</script>
