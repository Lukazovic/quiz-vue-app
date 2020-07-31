<template>
  <div id="app">
    <Header :numCorrect="numCorrect" :numTotal="numTotal" />
    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="3" v-if="!questions.length">
          <h3>Loading...</h3>
        </b-col>
        <b-col sm="6" offset="3" v-else-if="questions.length && numTotal < 10">
          <QuestionBox
            :currentQuestion="questions[index]"
            :next="next"
            :increment="increment"
          />
        </b-col>
        <b-col sm="6" offset="3" v-else-if="questions.length && numTotal >= 10">
          <h3>You answered correctly {{ numCorrect }} questions</h3>
          <h3>out of {{ numTotal }} questions</h3>
          <b-button @click="reset" variant="success">Restart</b-button>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from './components/Header.vue';
import QuestionBox from './components/QuestionBox.vue';

export default {
  name: 'App',
  components: {
    Header,
    QuestionBox,
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
    };
  },
  methods: {
    next() {
      this.index++;
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect++;
      }
      this.numTotal++;
    },
    reset() {
      this.questions = [];
      this.index = 0;
      this.numCorrect = 0;
      this.numTotal = 0;
      this.getNewQuestions();
    },
    getNewQuestions() {
      fetch('https://opentdb.com/api.php?amount=10&category=27&type=multiple', {
        method: 'get',
      })
        .then(response => {
          return response.json();
        })
        .then(jsonData => {
          this.questions = jsonData.results;
        });
    },
  },
  mounted() {
    this.getNewQuestions();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
