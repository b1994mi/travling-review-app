<template>
  <!-- Form to input and post request review to DB -->
  <review-form @suksesTambah="pushReviewToArr" />

  <!-- Button to refresh the review cards -->
  <div
    :class="[isLoading ? 'd-none' : 'd-flex', 'mt-3 justify-content-center']"
  >
    <button
      @click="getReviews"
      class="btn btn-outline-primary btn-responsive d-flex align-items-center"
    >
      <refresh-icon />
      <span class="ms-2">Refresh</span>
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
        :key="review.id"
        :id="review.id"
        :comment="review.review_comment"
        :name="review.name"
        :created-at="review.createdAt"
        :updated-at="review.updatedAt"
        :stars="review.review_star"
        :images="review.Images"
        @suksesHapus="removeReviewFromArr"
      />
    </div>
  </div>
</template>

<script>
import ReviewForm from "./ReviewForm";
import ReviewCard from "./ReviewCard";
import RefreshIcon from "./RefreshIcon.vue";
import { mainFetchURL } from "@/const.js";
export default {
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
  created() {
    this.getReviews();
  },
  methods: {
    async getReviews() {
      try {
        this.isLoading = true;
        const response = await fetch(mainFetchURL);
        const { status, message, data } = await response.json();
        if (status !== 200) throw message;
        let r = this.reviewImagesToFile(data);
        this.reviews = r.sort((a, b) => b.id - a.id);
        this.isLoading = false;
      } catch (error) {
        window.alert(error);
        this.isLoading = false;
      }
    },
    async pushReviewToArr({ data: { id } }) {
      try {
        this.reviews.unshift({
          id: 0,
          name: "Loading...",
          review_comment: "Loading...",
          review_star: 0,
          createdAt: "",
          updatedAt: "",
          Images: [],
        });
        const response = await fetch(`${mainFetchURL}/${id}`);
        const { status, message, data } = await response.json();
        this.reviews.shift();
        if (status !== 200) throw message;
        let r = data;
        r.Images.length > 0 &&
          (r.Images = r.Images.map((i) => this.imgToFile(i)));
        this.reviews = [r, ...this.reviews];
      } catch (error) {
        window.alert(error);
      }
    },
    removeReviewFromArr(id) {
      this.reviews = this.reviews.filter((el) => el.id != id);
    },
    reviewImagesToFile(review) {
      return review.map((review) => {
        if (review.Images.length > 0) {
          review.Images = review.Images.map((img) => {
            return this.imgToFile(img);
          });
        }
        return review;
      });
    },
    imgToFile({ buffer: { data }, originalname, id }) {
      let f = new File([new Uint8Array(data)], originalname);
      f.id = id;
      return f;
    },
  },
};
</script>

<style>
</style>