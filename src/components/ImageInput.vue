<template>
  <div>
    <button class="btn btn-primary btn-responsive" @click="launchFilePicker()">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="16"
        height="16"
        fill="currentColor"
        class="bi bi-image-fill"
        viewBox="0 0 16 16"
      >
        <path
          d="M.002 3a2 2 0 0 1 2-2h12a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2h-12a2 2 0 0 1-2-2V3zm1 9v1a1 1 0 0 0 1 1h12a1 1 0 0 0 1-1V9.5l-3.777-1.947a.5.5 0 0 0-.577.093l-3.71 3.71-2.66-1.772a.5.5 0 0 0-.63.062L1.002 12zm5-6.5a1.5 1.5 0 1 0-3 0 1.5 1.5 0 0 0 3 0z"
        />
      </svg>
      <span class="ms-2">Select image...</span>
    </button>
    <input
      ref="file"
      type="file"
      class="d-none"
      accept="image/jpeg,image/png,image/webp,image/gif"
      multiple
      @change="onFileChange"
    />
    <div :key="sesuatu" class="d-flex flex-wrap">
      <div
        v-for="img in images_files"
        :key="img"
        class="overflow-hidden mt-3 me-3 rounded d-flex align-items-center image-size position-relative"
      >
        <button
          type="button"
          class="btn btn-danger ms-1 mt-1 p-0 rounded-circle d-flex bg-gradient position-absolute fixed-top"
          @click="deleteImage(img)"
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
        <img :src="urlizer(img)" class="w-100" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "image-input",
  data() {
    return {
      images_files: this.images,
      errorDialog: null,
      errorText: "",
      uploadFieldName: "file",
      maxSize: 1048576, // 1 megabytes
      sesuatu: 0,
    };
  },
  props: ["images"],
  emits: ["listImgChanges"],
  methods: {
    launchFilePicker() {
      this.$refs.file.click();
    },
    onFileChange(event) {
      let tempArr = [...this.images_files, ...event.target.files];
      if (tempArr.length > 4) {
        return alert("Total gambar tidak boleh lebih dari 4!");
      }
      this.images_files = tempArr;
      this.$emit("listImgChanges", this.images_files);
      this.$refs.file.value = "";
    },
    urlizer(img) {
      return img instanceof Blob ? URL.createObjectURL(img) : img;
    },
    deleteImage(img) {
      this.images_files = this.images_files.filter((item) => item != img);
      this.$emit("listImgChanges", this.images_files);
    },
  },
};
</script>