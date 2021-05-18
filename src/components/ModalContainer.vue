<template>
  <div class="modal-mask" @click.self="$emit('close')">
    <div class="justify-content-center align-items-center modal-container">
      <div
        class="image-container position-relative"
        :style="'background-image: url(' + imgModal + ')'"
      >
        <button
          type="button"
          class="btn btn-danger ms-1 mt-1 p-0 rounded-circle d-flex bg-gradient position-absolute fixed-top"
          @click="$emit('close')"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            fill="currentColor"
            class="bi bi-x"
            viewBox="0 0 16 16"
          >
            <path
              d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708z"
            />
          </svg>
        </button>
        <img :src="imgModal" class="image-modal" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["imgModal"],
};
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 1;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-container {
  display: flex;
  flex: 1 0 90%;
  z-index: 2;
}

.image-container {
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center center;
}

.image-modal {
  opacity: 0;
  max-width: 90%;
  max-height: 90%;
}
</style>