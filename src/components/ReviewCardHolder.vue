<template>
  <div class="p-3">
    <div v-if="reviews.length">
      <review-card
        v-for="review in reviews"
        :key="review.id"
        :id="review._id"
        :comment="review.review_comment"
        :name="review.name"
        :date-created="review.created_at"
        :stars="review.review_star"
        :images="review.image"
      />
    </div>
    <div v-else class="d-flex justify-content-center">
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
  </div>
</template>

<script>
import ReviewCard from "./ReviewCard";
export default {
  data() {
    return {
      reviews: [],
    };
  },
  mounted() {
    fetch("https://review-backend.herokuapp.com/api/v1/review/")
      .then((r) => r.json())
      .then((d) => {
        d.data.reverse();
        this.reviews = [...d.data];
        console.log(d);
      });
  },
  components: {
    ReviewCard,
  },
};
</script>