<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="onLoad">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error">{{ error }}</p>
      <p v-else-if="!isLoading && (!results || results.length < 1)">No stored experiences found. For start add any surveys data.</p>
      <ul v-else-if="!isLoading && results && results.length > 0">
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
  mounted () {
    this.onLoad();
  },
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null
    }
  },
  methods: {
    onLoad() {
      this.isLoading = true;
      fetch('https://vue-test-http-a9f40-default-rtdb.firebaseio.com/surveys.json')
      .then(res => res.json())
      .then(data => {
        const results = [];
        this.isLoading = false;

        for (const id in data) {
          results.push({
            id: id,
            name: data[id].name,
            rating: data[id].rating
          });
        }

        this.results = results;
      })
      .catch(error => {
        console.log(error);
        this.isLoading = false;
        this.error = 'Failed to fetch! Try again later.';
      });
    }
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