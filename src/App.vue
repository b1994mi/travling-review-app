<template>
  <review-form />
  <review-card-holder />
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1" crossorigin="anonymous">
</template>

<script>
import ReviewForm from "./components/ReviewForm";
import ReviewCardHolder from "./components/ReviewCardHolder";

export default {
  name: "App",
  components: {
    ReviewForm,
    ReviewCardHolder,
  },
};
</script>