<template>
  <review-form @suksesTambah="reloadReviewCardHolder" />
  <div class="d-flex justify-content-center">
    <button @click="reloadReviewCardHolder" class="btn btn-primary"><refresh-icon /> Refresh</button>
  </div>
  <review-card-holder :key="keyReviewCardHolder" />
  <link
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css"
    rel="stylesheet"
    integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1"
    crossorigin="anonymous"
  />
</template>

<script>
import ReviewForm from "./components/ReviewForm";
import ReviewCardHolder from "./components/ReviewCardHolder";
import RefreshIcon from './components/RefreshIcon.vue';

export default {
  name: "App",
  data() {
    return {
      keyReviewCardHolder: false,
    };
  },
  components: {
    ReviewForm,
    ReviewCardHolder,
    RefreshIcon,
  },
  methods: {
    reloadReviewCardHolder() {
      this.keyReviewCardHolder = !this.keyReviewCardHolder;
    },
  },
};
</script>