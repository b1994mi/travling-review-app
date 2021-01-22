<template>
  <form
    @submit.prevent="handleSubmit"
    id="form-utama"
    class="d-flex flex-column px-3 pt-2 pb-3 border-bottom"
  >
    <div class="d-flex flex-wrap mb-3">
      <h1 style="font-size: 3em">Review</h1>
      <div class="d-flex flex-grow-1 justify-content-end">
        <rating
          :key="keyRating"
          :grade="0"
          :maxStars="5"
          :hasCounter="false"
          @update="getStars"
        />
      </div>
    </div>
    <div class="form-floating mb-3">
      <input
        id="nama-pengulas"
        class="form-control"
        type="text"
        placeholder="Nama Lengkap"
        :disabled="isLoading"
        v-model="nama"
      />
      <label for="nama-pengulas">Nama Lengkap</label>
    </div>
    <div class="form-floating">
      <textarea
        id="isi-ulasan"
        class="mb-3 form-control"
        style="height: 7rem"
        form="form-utama"
        placeholder="Tulis Review Terbaikmu"
        :disabled="isLoading"
        v-model="review"
      />
      <label for="isi-ulasan">Tulis Review Terbaikmu</label>
    </div>
    <div class="d-flex justify-content-evenly flex-wrap">
      <div class="flex-shrink-1 mb-3 mb-sm-0" style="flex-basis:480px">
        <input
          type="file"
          nama="images"
          id="unggah-file"
          ref="myFileInput"
          class="form-control"
          multiple
          :disabled="isLoading"
        />
      </div>
      <input type="submit" value="Kirim" class="btn btn-primary" />
    </div>
  </form>
</template>

<script>
import Rating from "./Rating";
export default {
  data() {
    return {
      nama: "",
      review: "",
      bintang: "",
      isLoading: false,
      keyRating: false,
    };
  },
  emits: ["suksesTambah"],
  methods: {
    getStars(s) {
      this.bintang = s;
    },
    handleSubmit(event) {
      if (!this.nama || !this.review || !this.bintang) {
        if (!this.nama) {
          window.alert("mohon lengkapi nama!");
        }
        if (!this.review) {
          window.alert("mohon lengkapi isi ulasan Anda!");
        }
        if (!this.bintang) {
          window.alert("mohon lengkapi rating bintang Anda!");
        }
      } else {
        let formdata = new FormData();
        let unggahan = event.target.querySelector("#unggah-file");
        formdata.append("name", this.nama);
        formdata.append("review_comment", this.review);
        formdata.append("review_star", this.bintang);
        unggahan.files.forEach((item) => {
          formdata.append("images", item, item.name);
        });
        this.isLoading = true;
        fetch("http://localhost:5050/api/v1/review", {
          method: "POST",
          body: formdata,
        })
          .then((response) => response.json())
          .then((result) => {
            this.$emit("suksesTambah", result);
            this.isLoading = false;
            this.nama = "";
            this.review = "";
            this.bintang = "";
            this.$refs.myFileInput.value = "";
            this.keyRating = !this.keyRating;
          })
          .catch((error) => {
            window.alert(error);
            this.isLoading = false;
          });
      }
    },
  },
  components: {
    Rating,
  },
};
</script>