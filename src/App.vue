<template>
  <div class="container-app">
        <div class="container-quiz">

          <div class="header-quiz" v-for="(category,index) in results.slice(a,b)" :key="index" v-show="quiz">
            <h1>{{category.category}}</h1>
          </div>
          <div class="step-progress" :style="{'width':progress + '%'}"></div>
          <br>
          <div class="box" v-for="(question,index) in results.slice(a,b)" :key="index" v-show="quiz">
              <div class="box-question">
                <h4>{{question.question}}</h4>
              </div>
              <div class="box-propositions">
                <ul>
                  <li v-for="(incorrect_answers,index) in question.incorrect_answers" :key="index" class="li" 
                  @click="selectResponse(incorrect_answers,index)" :class=" correct ? 
                  check(incorrect_answers) : ''">{{incorrect_answers}} 
                  <div class="fas fa-check" v-if="correct_answer ?  incorrect_answers.correct: ''"></div>
                  <div class="fas fa-times" v-if="correct_answer ?  !incorrect_answers.correct: ''"></div></li>     
                </ul>
              </div>
              
              <div class="box-number">
                <h2>{{b}} of {{results.length}}</h2>
              </div>
              
          </div>
          <div class="box-score" v-if="score_show">
              
              
              <h2>Your score is</h2>
              <h2>{{score}}/{{results.length}}</h2>
              <div class="btn-restart">
                  <button @click="restartQuiz">Restart <i class="fas fa-sync-alt"></i></button>
              </div>
          </div>
          <div class="footer-quiz">
                <div v-if="progress < 100" class="box-button">
                    <button  @click="skipQuestion()" :style="next ? 'background-color: rgb(106, 128, 202)' : ''">Skip</button>
                    <button  @click="nextQuestion()" :style="!next ? 'background-color: rgb(106, 128, 202)' : ''">Next</button>
                </div>             
          </div>            
        </div>
  </div>
</template>

<script>
//import HelloWorld from './components/HelloWorld.vue'
import axios from "axios";

const path = "api.php?amount=10&difficulty=hard&type=boolean"

export default {
  data(){
    return{
      results: [],
      data: {
        category: null,
        type: null,
        difficulty: null,
        question: null,
        correct_answer: null,
        incorrect_answers: null
      },
      a:0,
      b:1,
      next:true,
      score_show:false,
      quiz:true,
      score:0,
      correct:false,
      progress:0,
      
    }
  },
  name: 'App',
  components: {
    //HelloWorld
  },
  computed:{
      
  },
  methods:{

    Showquestions(){
      axios.get(this.$store.state.backURL + path, {
      })
      .then(response => {
          this.results = response.data.results;
          console.log(this.results);
          this.data = this.results[0]
      })
      .catch(err => {
          alert(err);
      })
    },
    selectResponse(e){
      this.correct = true;
      this.next = false;
      if (e.correct) {
        this.score++;
      }
    },
    check(status){
        if (status.correct) {
          return 'correct'
        }else{
          return 'incorrect' 
        }
    },
    nextQuestion(){
      if (this.next) {
        return;
      }
      this.progress = this.progress + (100/this.results.length);
      if(this.results.length - 1 == this.a){
        this.score_show = true;
        this.quiz = false;    
      }
      else{
        this.a++;
        this.b++;
        this.correct = false;
        this.next = true;
        
      }
      
    },
    skipQuestion(){
      if (!this.next) {
        return;
      }
      this.progress = this.progress + (10/this.results.length);
      if(this.results.length - 1 == this.a){
        this.score_show = true;
        this.quiz = false;
        
        
      }else{
        this.a++;
        this.b++;
        
      }
      
    },
    
    restartQuiz(){
      
      location.reload() // reset data in vue
       
    } 
  },
  mounted(){
    this.Showquestions();
  }
}
</script>

<style scoped>
.box-question{
  width: 50%;
  text-align: center;
  border: 1px solid #000000;
}
</style>