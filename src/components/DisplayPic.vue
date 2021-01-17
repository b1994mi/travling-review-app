<template>
  <div class="d-flex flex-column">
    <div class="rounded-circle overflow-hidden me-3 avatar-size">
      <img :src="picURL" :alt="picName" class="w-100 h-100" />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    picURL: String,
    picName: String,
  },
};
</script>

<style>
.avatar-size {
  width: 30px;
  height: 30px;
}
@media (min-width: 310px) {
  .avatar-size {
    width: 40px;
    height: 40px;
  }
}
@media (min-width: 576px) {
  .avatar-size {
    width: 60px;
    height: 60px;
  }
}
</style>