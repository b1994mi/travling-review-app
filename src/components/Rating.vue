// Rating component by ramazanerikli
<template>
  <ul class="d-flex">
    <div
      @click="rate(star)"
      v-for="star in maxStars"
      :class="{ active: star <= stars }"
      :key="star.stars"
      class="star"
    >
      <div v-if="star <= stars" class="ms-2">
        <stars-filled :height="32" :width="32" />
      </div>
      <div v-else class="ms-2">
        <stars-hollow :height="32" :width="32" />
      </div>
    </div>
  </ul>
  <div v-if="hasCounter" class="info counter">
    <span class="score-rating">{{ stars }}</span>
    <span class="divider">/</span>
    <span class="score-max">{{ maxStars }}</span>
  </div>
</template>

<script>
import StarsFilled from "./StarsFilled";
import StarsHollow from "./StarsHollow";
export default {
  name: "Rating",
  props: ["grade", "maxStars", "hasCounter"],
  emits: ["update"],
  data() {
    return {
      stars: this.grade,
    };
  },
  methods: {
    rate(star) {
      if (typeof star === "number" && star <= this.maxStars && star >= 0) {
        this.stars = this.stars === star ? star - 1 : star;
        this.$emit("update", this.stars);
      }
    },
  },
  components: {
    StarsFilled,
    StarsHollow,
  },
};
</script>