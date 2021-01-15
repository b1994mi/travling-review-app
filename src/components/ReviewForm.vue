<template>
  <form
    @submit.prevent="handleSubmit"
    id="form-utama"
    class="d-flex flex-column p-3"
  >
    <div class="d-flex">
      <h1>Review</h1>
      <div class="d-flex flex-grow-1 justify-content-end">
        <rating
          :grade="0"
          :maxStars="5"
          :hasCounter="false"
          @update="getStars"
        />
      </div>
    </div>
    <input
      class="mb-3"
      type="text"
      placeholder="Tulis Nama Kamu"
      required
      v-model="nama"
    />
    <textarea
      class="mb-3"
      form="form-utama"
      placeholder="Tulis Review Terbaikmu"
      required
      v-model="review"
    />
    <div class="d-flex justify-content-evenly flex-wrap">
      <input type="file" nama="images" id="unggah-file" multiple />
      <input type="submit" value="Kirim" />
    </div>
  </form>
  <p>nama: {{ nama }} dgn review: {{ review }} dan bintang {{ bintang }}</p>
</template>

<script>
import Rating from "./Rating";
export default {
  data() {
    return {
      nama: "",
      review: "",
      bintang: "",
    };
  },
  methods: {
    handleSubmit(event) {
      let formdata = new FormData();
      let unggahan = event.target.querySelector("#unggah-file");
      formdata.append("name", this.nama);
      formdata.append("review_comment", this.review);
      formdata.append("review_star", this.bintang);
      unggahan.files.forEach((item) => {
        formdata.append("images", item, item.name);
      });
      fetch("https://review-backend.herokuapp.com/api/v1/review/", {
        method: "POST",
        body: formdata,
      })
        .then((response) => response.text())
        .then((result) => console.log(result))
        .catch((error) => console.log("error", error));
    },
    getStars(s) {
      this.bintang = s;
    },
  },
  components: {
    Rating,
  },
};
</script>