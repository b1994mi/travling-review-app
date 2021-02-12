<template>
  <div id="form-utama" class="d-flex flex-column px-3 pt-2 pb-3 border-bottom">
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
    <div class="form-floating mb-3 card-text-size">
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
    <div class="form-floating card-text-size">
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
    <image-input :images="gambar" @listImgChanges="setImgs" :key="keyImgInput">
      <button
        class="btn btn-primary btn-responsive d-flex align-items-center"
        @click="handleSubmit"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="currentColor"
          class="bi bi-upload"
          viewBox="0 0 16 16"
        >
          <path
            d="M.5 9.9a.5.5 0 0 1 .5.5v2.5a1 1 0 0 0 1 1h12a1 1 0 0 0 1-1v-2.5a.5.5 0 0 1 1 0v2.5a2 2 0 0 1-2 2H2a2 2 0 0 1-2-2v-2.5a.5.5 0 0 1 .5-.5z"
          />
          <path
            d="M7.646 1.146a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1-.708.708L8.5 2.707V11.5a.5.5 0 0 1-1 0V2.707L5.354 4.854a.5.5 0 1 1-.708-.708l3-3z"
          />
        </svg>
        <span class="ms-2">Post Review</span>
      </button>
    </image-input>
  </div>
</template>

<script>
import ImageInput from "./ImageInput";
import Rating from "./Rating";
export default {
  data() {
    return {
      nama: "",
      review: "",
      bintang: "",
      gambar: [],
      isLoading: false,
      keyRating: false,
      keyImgInput: false,
      mainFetchURL: "https://secure-mountain-83151.herokuapp.com/api/v1/review",
    };
  },
  emits: ["suksesTambah"],
  methods: {
    getStars(s) {
      this.bintang = s;
    },
    setImgs(arr) {
      this.gambar = arr;
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
        formdata.append("name", this.nama);
        formdata.append("review_comment", this.review);
        formdata.append("review_star", this.bintang);
        this.gambar.forEach((item) => {
          formdata.append("images", item, item.name);
        });
        this.isLoading = true;
        fetch(this.mainFetchURL, {
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
            this.gambar = [];
            this.keyRating = !this.keyRating;
            this.keyImgInput = !this.keyImgInput;
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
    ImageInput,
  },
};
</script>