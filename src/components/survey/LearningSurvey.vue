<template>
  <section>
    <base-card>
      <h2>How was you learning experience? </h2>
      <form @submit.prevent="submitSurvey">
        <div class="form-control">
          <!-- <label for="name">Your Name</label> -->
          <input type="text" class="name-input" id="name" name="name" placeholder="Enter your name" v-model.trim="enteredName" />
        </div>
        <h3>My learning experience was ...</h3>
        <div class="form-control">
          <input type="radio" id="rating-poor" value="poor" name="rating" v-model="chosenRating" />
          <label for="rating-poor">Poor</label>
        </div>
        <div class="form-control">
          <input
            type="radio"
            id="rating-average"
            value="average"
            name="rating"
            v-model="chosenRating"
          />
          <label for="rating-average">Average</label>
        </div>
        <div class="form-control">
          <input type="radio" id="rating-great" value="great" name="rating" v-model="chosenRating" />
          <label for="rating-great">Great</label>
        </div>
        <base-dialog v-if="invalidInput" title="Invalid Input" @close="closeDialog">
          <template #body>
            <p>One or more input fields are invalid. Please check your provided data.</p>
          </template>
          <template #actions>
            <base-button @click="closeDialog">Ok</base-button>
          </template>
        </base-dialog>
        <base-dialog v-if="error" title="Error" @close="closeDialog">
          <template #body>
            <p> Could not submit data. Try again</p>
          </template>
          <template #actions @close="closeDialog"> 
            
          </template>
        </base-dialog>
        <div>
          <base-button>Submit</base-button>
        </div>
      </form>
    </base-card>
  </section>
</template>

<script>
import BaseButton from '../UI/BaseButton.vue';
import BaseDialog from '../UI/BaseDialog.vue';
export default {
	components: { BaseDialog, BaseButton },
  data() {
    return {
      enteredName: '',
      chosenRating: null,
      invalidInput: false,
      error: null,
      dataError: false,
    };
  },
  // emits: ['survey-submit'],
  methods: {
    submitSurvey() {
      if (this.enteredName === '' || !this.chosenRating) {
        this.invalidInput = true;
        return;
      }
      this.invalidInput = false;

      // this.$emit('survey-submit', {
      //   userName: this.enteredName,
      //   rating: this.chosenRating,
      // });
      this.error= null;
      fetch("https://vue-http-demo-d63e9-default-rtdb.firebaseio.com/surveys.json?auth=Hjxy3x2TIa1AzIvmA2TZPdYUB825OiTne7MsXhNX", {
        method: 'POST',
        headers: { 
          'Content-Type' : 'application/json'
        },
        body: JSON.stringify({name: this.enteredName, rating: this.chosenRating}),
      }).then(response=> {
        if(response.ok){
          //...
        }
        else{
          throw new Error("Could not submit data. Try again")
        }
      })
      .catch((error) => {
        console.log(error);
        this.error= error.message;
      });

      this.enteredName = '';
      this.chosenRating = null;
    },
    closeDialog(){
      this.invalidInput= false;
      this.error= null;
      
    }, //methods
  },
};
</script>

<style scoped>
.form-control {
  margin: 0.5rem 0;

}

input[type='text'] {
  display: block;
  width: 25rem;
  margin-top: 0.5rem;
}
.name-input {
  border-bottom: 2px solid #330075;
  border-top-style: hidden;
  border-right-style: hidden;
  border-left-style: hidden;
  padding: 0.4rem;
  outline: none; 
}
.name-input:focus {
  font-size: 30px ;
}
</style>