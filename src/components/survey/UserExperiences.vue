  <template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click= "loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading...</p>
      <base-dialog v-else-if="!isLoading && error" title="Error" @close="closeDialog">
          <template #body>
            <p> {{error}}</p>
          </template>
          <template #actions @close="closeDialog"> 
            
          </template>
        </base-dialog>
      <p v-else-if="!isLoading  && (!results || results.length === 0)">No stored experience found</p>
      <ul v-else-if="!isLoading && results.length> 0 && results">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>  
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data(){
    return{
      results: [],
      isLoading: false,
      error: null
    };
  },
  methods: {
    loadExperiences(){
      this.isLoading= true;
      this.error= null;
      fetch("https://vue-http-demo-d63e9-default-rtdb.firebaseio.com/surveys.json?auth=Hjxy3x2TIa1AzIvmA2TZPdYUB825OiTne7MsXhNX")
        .then((response) => {
          if(response.ok){
            return response.json();
          }
        })
      .then((data) => {
        this.isLoading= false;
        const results = []
        for(const id in data){
          results.push({
            id: id,
            name: data[id].name,
            rating: data[id].rating,
          });
        }
        this.results= results;
      })
      .catch((error) => {
        console.log(error);
        this.isLoading= false;
        this.error= "Failed to fetch data- Please try again later."
      });
    },
    closeDialog(){
      this.error= null;
    },
  }, //methods
  mounted(){
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>