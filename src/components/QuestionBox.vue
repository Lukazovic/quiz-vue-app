<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />

      <b-list-group
        v-for="(answer, index) in answers"
        :key="index"
        @click="!answered && selectAnswer(index)"
      >
        <b-list-group-item :class="answerClass(index)">{{
          answer
        }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
        >Submit</b-button
      >
      <b-button @click="next" variant="success" :disabled="!answered"
        >Next</b-button
      >
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash';

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
      answered: false,
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.suffleAnswers();
      },
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;

      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }

      this.answered = true;

      this.increment(isCorrect);
    },
    suffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answerClass(index) {
      let answerClass = '';

      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected';
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct';
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = 'incorrect';
      }
      return answerClass;
    },
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 5px;
}

.list-group-item:hover {
  background-color: #eee;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background-color: lightblue;
}
.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: rgb(245, 84, 84);
}
</style>
