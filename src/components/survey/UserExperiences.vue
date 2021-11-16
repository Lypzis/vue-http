<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <ul v-if="!isLoading && results.length > 0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
      <p v-else-if="!isLoading && error">{{ error }}</p>
      <p v-else-if="!isLoading && results.length === 0">
        No stored experiences found. Start adding some ratings :D
      </p>
      <p v-else>Loading...</p>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null
    };
  },
  mounted() {
    this.loadExperiences();
  },
  methods: {
    async loadExperiences() {
      this.isLoading = true;
      this.error = null;
      try {
        const resultsFetched = await fetch(
          'https://vue-http-demo-5f3b7-default-rtdb.firebaseio.com/surveys.json'
        );

        if (!resultsFetched.ok) {
          throw 'Nothing was retrieved.';
        }

        const results = [];
        const data = await resultsFetched.json();

        for (const id in data) {
          results.push({
            id,
            name: data[id].name,
            rating: data[id].rating
          });
        }

        this.results = results;

        this.isLoading = false;
      } catch (err) {
        console.log(err);
        this.error = 'Failed to fetch data, please try again later.';
        this.isLoading = false;
      }
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
