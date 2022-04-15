<template>
  <div>
    <b-jumbotron>
      <template #lead>
        {{ currentQuestion.question }}
      </template>

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || isAnswered"
      >
        Submit
      </b-button>
      <b-button @click="next" variant="success" :hidden="lastQuestion" :disabled="!isAnswered"> Next </b-button>
      <b-button @click="reset" variant="danger" :hidden="!lastQuestion" :disabled="!isAnswered"> Reset </b-button>
    </b-jumbotron>
  </div>
</template>

<script>
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    reset: Function,
    increment: Function,
    questionNumber: Number
  },
  data() {
    return {
      selectedIndex: null,
      isAnswered: false,
      correctAnswer: null,
      correctIndex: null,
      lastQuestion: false,
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.answers];
      return answers;
    },
  },
  watch: {
    currentQuestion() {
      this.selectedIndex = null;
      this.isAnswered = false;
    },
    questionNumber(){
      if(this.questionNumber === 4){
        this.lastQuestion = true;
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;
      this.isAnswered = true;

      const body = {
        question: this.currentQuestion.question,
        answer: this.currentQuestion.answers[this.selectedIndex],
      };

      fetch("http://localhost:8080/checkanswer", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(body),
      })
        .then((response) => {
          return response.json();
        })
        .then((jsonData) => {
          this.isCorrect = jsonData.correct;
          this.correctAnswer = jsonData.correctAnswer;
          this.increment(jsonData.correct);
          this.correctIndex = this.currentQuestion.answers.indexOf(
            this.correctAnswer
          );
        });
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.isAnswered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.isAnswered && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.isAnswered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      }
      return answerClass;
    },
  },
  mounted() {},
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.btn {
  margin: 0 5px;
}

.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: greenyellow;
}

.incorrect {
  background-color: red;
}
</style>