<template>
  <div>
    <button class="btn btn-primary" @click="launchFilePicker()">
      Select Image...
    </button>
    <input
      ref="file"
      type="file"
      accept="image/jpeg,image/png,image/webp,image/gif"
      multiple
      @change="onFileChange"
    />
    <div :key="sesuatu" class="d-flex flex-wrap">
      <div
        v-for="img in images_files"
        :key="img"
        class="overflow-hidden me-3 rounded d-flex align-items-center image-size"
      >
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
  methods: {
    launchFilePicker() {
      this.$refs.file.click();
    },
    onFileChange(event) {
      this.images_files = [...this.images_files, ...event.target.files];
      this.$emit("listImgChanges", this.images_files);
      console.log(this.images_files)
    },
    urlizer(img) {
      console.log(img)
      return img instanceof Blob ? URL.createObjectURL(img) : img;
    },
  },
};
</script>