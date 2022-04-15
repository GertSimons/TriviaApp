<template>
  <div id="app">
    <Header :numberCorrect="numberCorrect" :numberTotal="numberTotal" />

    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="3">
          <QuestionBox
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :next="next"
            :reset="reset"
            :increment="increment"
            :questionNumber="this.index"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import QuestionBox from "./components/QuestionBox.vue";

export default {
  name: "App",
  components: {
    Header,
    QuestionBox,
  },
  data() {
    return {
      questions: [],
      index: 0,
      numberCorrect: 0,
      numberTotal: 0,
      token: ''
    };
  },
  methods: {
    next() {
      this.index++;
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numberCorrect++;
      }
      this.numberTotal++;
    },
    reset() {
      this.questions = [];
      this.index = 0;
      this.numberCorrect = 0;
      this.numberTotal = 0;

      fetch("http://localhost:8080/questions?quantity=5&token=" + this.token, {
        method: "get",
      })
        .then((response) => {
          return response.json();
        })
        .then((jsonData) => {
          this.questions = jsonData.results;
        });
    },
  },
  mounted: function () {
    fetch("http://localhost:8080/questions?quantity=5&token=", {
      method: "get",
    })
      .then((response) => {
        return response.json();
      })
      .then((jsonData) => {
        this.questions = jsonData.results;
        this.token = jsonData.token
      });
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
