<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!loading && error">{{ error }}</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No experiences found, please start adding some reviews.
      </p>

      <ul v-else>
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
  mounted() {
    this.loadExperiences();
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null,
    };
  },
  components: {
    SurveyResult,
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.error = null;
      fetch(
        'https://vue-http-demo-3c262-default-rtdb.firebaseio.com/surveys.json'
      )
        .then((res) => {
          if (res.ok) {
            return res.json();
          }
        })
        .then((data) => {
          const results = [];
          this.isLoading = false;
          for (const id in data) {
            results.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating,
            });
          }

          this.results = results;
        })
        .catch((err) => {
          console.log(err);
          this.isLoading = false;
          this.error = 'Failed to fetch data, please try again later.';
        });
    },
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>