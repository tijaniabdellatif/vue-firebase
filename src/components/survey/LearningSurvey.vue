<template>
  <section>
    <base-card>
      <h2>How was you learning experience?</h2>
      <form @submit.prevent="submitSurvey">
        <div class="form-control">
          <label for="name">Your Name</label>
          <input type="text" id="name" name="name" v-model.trim="enteredName" />
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
        <p
          v-if="invalidInput"
        >One or more input fields are invalid. Please check your provided data.</p>

        <p v-if="errorResponse">
           {{errorResponse.type}}
          <span style='color:red'> {{errorResponse.status}}</span>
          <span style='color:green'> {{errorResponse.text}}</span>

        </p>
        <div>
          <base-button>Submit</base-button>
        </div>
      </form>
    </base-card>
  </section>
</template>

<script>
export default {

  data() {
    return {
      enteredName: '',
      chosenRating: null,
      invalidInput: false,
      endpoint : 'https://vue-http-demo-4be48-default-rtdb.firebaseio.com/',
      errorResponse : null
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


      this.fetchData(this.enteredName,this.chosenRating,'POST');

      this.enteredName = '';
      this.chosenRating = null;
    },

    fetchData(username,rating,type)
    {

      this.errorResponse = null;
        fetch(this.endpoint+'/surveys.json',{

          method:type,
          headers:{
            'Content-type':'application/json'
          },

          body:{
            name:username,
            rating:rating
          }
        })

          .then((response) => {

                   if(response.ok){

                      console.log('good');
                   }
                   else{
                     throw new Error('Could not save the data');
                   }
          })

          .catch((error) => {

              this.errorResponse = {

                   type:'Server Error',
                   status:400,
                   text:error.message
              };
        })

      }
  },
};
</script>

<style scoped>
.form-control {
  margin: 0.5rem 0;
}

input[type='text'] {
  display: block;
  width: 20rem;
  margin-top: 0.5rem;
}
</style>
