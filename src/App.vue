<template>
  <!-- Bootstrap CSS CDN -->
  <link
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css"
    rel="stylesheet"
    integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1"
    crossorigin="anonymous"
  />

  <!-- Form to input and post request review to DB -->
  <review-form @suksesTambah="pushReviewToArr" />

  <!-- Button to refresh the review cards -->
  <div :class="[isLoading ? 'd-none' : 'd-flex', 'mt-3 justify-content-center']">
    <button @click="getReviews" class="btn btn-primary">
      <refresh-icon /> Refresh
    </button>
  </div>

  <!-- Review Cards -->
  <div class="p-3">
    <div v-if="isLoading" class="d-flex justify-content-center">
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
    <div v-else>
      <review-card
        v-for="review in reviews"
        :key="review._id"
        :id="review._id"
        :comment="review.review_comment"
        :name="review.name"
        :date-created="review.created_at"
        :stars="review.review_star"
        :images="review.image"
        @suksesHapus="removeReviewFromArr"
      />
    </div>
  </div>
</template>

<script>
import ReviewForm from "./components/ReviewForm";
import ReviewCard from "./components/ReviewCard";
import RefreshIcon from "./components/RefreshIcon.vue";

export default {
  name: "App",
  data() {
    return {
      keyReviewCardHolder: false,
      showRefresh: false,
      reviews: [],
      isLoading: true,
    };
  },
  components: {
    ReviewForm,
    ReviewCard,
    RefreshIcon,
  },
  mounted() {
    this.getReviews();
  },
  methods: {
    getReviews() {
      this.isLoading = true;
      fetch("https://review-backend.herokuapp.com/api/v1/review/")
        .then((response) => response.json())
        .then((result) => {
          this.reviews = [...result.data.reverse()];
          this.isLoading = false;
        })
        .catch((error) => {
          window.alert(error);
          this.isLoading = false;
        });
    },
    pushReviewToArr(object) {
      fetch(
        `https://review-backend.herokuapp.com/api/v1/review/${object.data.id}`
      )
        .then((response) => response.json())
        .then((result) => (this.reviews = [result.data, ...this.reviews]))
        .catch((error) => {
          window.alert(error);
          this.isLoading = false;
        });
    },
    removeReviewFromArr(object) {
      this.reviews = this.reviews.filter((el) => el._id != object.data.id);
    },
  },
};
</script>