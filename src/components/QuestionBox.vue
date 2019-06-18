<template>
  <div class="question-box">
    <b-jumbotron>
      <template slot="lead">{{currentQuestion.question}}</template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
          :disabled="submitted"
        >{{answer}}</b-list-group-item>
      </b-list-group>

      <b-button
        @click="submitAnswer"
        :disabled="selectedIndex === null || submitted"
        variant="primary"
      >Submit</b-button>
      <b-button @click="next" :disabled="!submitted || numTotal > 9" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
    numTotal: Number
  },
  data() {
    return {
      selectedIndex: null,
      answers: [],
      submitted: false,
      correctIndex: null
    };
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.submitted = false;
        this.answers = this.shuffleAnswers();
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;

      if (
        this.answers[this.selectedIndex] === this.currentQuestion.correct_answer
      ) {
        isCorrect = true;
      }

      this.submitted = true;

      this.increment(isCorrect);
    },
    shuffleAnswers() {
      const answers = _.shuffle([
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ]);

      this.correctIndex = answers.indexOf(this.currentQuestion.correct_answer);

      return answers;
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.submitted && index === this.selectedIndex) {
        answerClass = "selected";
      } else if (this.submitted && index === this.correctIndex) {
        answerClass = "correct";
      } else if (
        this.submitted &&
        this.selectedIndex === index &&
        index !== this.correctIndex
      ) {
        answerClass = "incorrect";
      }

      return answerClass;
    }
  }
};
</script>

<style scoped>
.question-box {
  margin-top: 15px;
}

.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #eee;
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
  background-color: lightcoral;
}
</style>
