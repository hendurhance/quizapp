<template>
   <div class="question-box-container">
     <b-jumbotron>
          <template slot="lead">
           {{ currentQuestion.question }}
          </template>

          <hr class="my-4">

          <b-list-group>
             <b-list-group-item
                v-for="(answer, index) in answers" :key="index" 
                @click.prevent="selectAnswer(index)"
                :class="answerClass(index)"
             >
             {{ answer }}
             </b-list-group-item>
           </b-list-group>


        <b-button 
           variant="primary" 
           @click="submitAnswer"
           :disabled="selectedIndex === null || answered"
        >
        Submit
        </b-button>
        <b-button @click="next" variant="success">
          Next
        </b-button>
     </b-jumbotron>
   </div>  
</template>


<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
    },
    data: function() {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },
    computed: {
    answers(){
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
  },
  methods: {
      selectAnswer(index){
        this.selectedIndex = index
      },
      submitAnswer() {
        let isCorrect = false

        if (this.selectedIndex === this.correctIndex) {
          isCorrect = true
        }
        this.answered = true

        this.increment(isCorrect)
      },
      shuffleAnswers(){
        let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
        this.shuffledAnswers = _.shuffle(answers)
        this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
      },
      answerClass(index){
        let answerClass = ''
        
        if(!this.answered && this.selectedIndex === index){
          answerClass = 'selected'
        }else if(this.answered && this.correctIndex === index){
          answerClass = 'correct'
        }else if (this.answered && this.selectedIndex === index && this.correctIndex !== index ){
          answerClass = 'incorrect'
        }

        return answerClass

      }
  }
}
</script>>

<style>
.bv-example-row{
    margin-top: 15px;
}
.list-group{
    margin-bottom: 15px;
}
.list-group-item:hover{
    background: #e2e2e2;
    cursor: pointer;
}
.btn{
    margin: 0 7em;
}

.selected{
    background-color: skyblue;
}
.correct{
    background-color: lightgreen;
}
.incorrect{
    background-color: lightcoral;
}

@media screen and (max-width: 500px){
  .btn{
    margin: 0 2em;
}
}
</style>