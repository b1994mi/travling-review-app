<template>
  <div class="card flex-row bg-light mb-2 p-3 rounded shadow-sm card-text-size">
    <display-pic :picURL="picURL" :picName="picName" />
    <div class="d-flex flex-wrap w-100">
      <div class="d-flex justify-content-between w-100">
        <div class="d-flex flex-column">
          <div v-if="isEditMode" class="d-flex flex-wrap">
            <input
              type="text"
              placeholder="Your Name..."
              class="mt-2 mb-2 mt-sm-0 mb-sm-0"
              v-model="name_toBeEdited"
              :disabled="isLoading"
            />
            <button
              @click="batalUbahReview"
              :disabled="isLoading"
              class="btn btn-secondary mt-2 mb-2 mt-sm-0 mb-sm-0 ms-3"
            >
              Batal
            </button>
            <button
              @click="fixUbahReview"
              :disabled="isLoading"
              class="btn btn-outline-danger ms-3 mt-2 mb-2 mt-sm-0 mb-sm-0"
            >
              Simpan
            </button>
          </div>
          <p v-else class="fw-bold m-0">{{ name_toBeShown }}</p>
          <p class="fw-light m-0">{{ formatTgl(dateCreated) }}</p>
          <rating
            :grade="stars_toBeEdited"
            :maxStars="5"
            :hasCounter="false"
            @update="setStars"
            v-if="isEditMode"
          />
          <p v-else class="m-0 mb-1">
            <stars-filled v-for="n in stars_toBeShown" :key="n" />
            <stars-hollow v-for="n in 5 - stars_toBeShown" :key="n" />
          </p>
        </div>
        <details v-if="!isEditMode" class="dropdown">
          <summary role="button">
            <a class="button">
              <three-dots />
            </a>
          </summary>
          <div class="bg-secondary bg-gradient px-3 py-1">
            <a
              @click="ubahReview"
              class="btn btn-warning bg-gradient rounded-pill p-1 mb-1"
              >Edit review</a
            >
            <a
              @click="hapusReview"
              class="btn btn-danger bg-gradient rounded-pill p-1"
              >Delete review</a
            >
          </div>
        </details>
      </div>
      <textarea
        v-if="isEditMode"
        v-model="comment_toBeEdited"
        class="form-control my-2 align-self-stretch"
        style="font-size: inherit !important; min-height: 8em"
        :disabled="isLoading"
      ></textarea>
      <div v-else class="d-flex flex-column">
        <p class="isi-review m-0 mb-1 text-break">{{ comment_toBeShown }}</p>
        <div v-if="images.length > 0" class="d-flex flex-wrap">
          <div
            class="overflow-hidden me-3 rounded d-flex align-items-center image-size"
            v-for="image in images"
            :key="image"
          >
            <img class="w-100" :src="'data:image/jpeg;base64,' + image.b64" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DisplayPic from "./DisplayPic.vue";
import Rating from "./Rating";
import StarsFilled from "./StarsFilled";
import StarsHollow from "./StarsHollow";
import ThreeDots from "./ThreeDots";
export default {
  data() {
    return {
      isEditMode: false,
      isLoading: false,
      name_toBeShown: this.name,
      comment_toBeShown: this.comment,
      stars_toBeShown: this.stars,
      name_toBeEdited: this.name,
      comment_toBeEdited: this.comment,
      stars_toBeEdited: this.stars,
      picURL:
        "https://abs.twimg.com/sticky/default_profile_images/default_profile_400x400.png", // Suatu saat ini bisa diganti prop
      picName: "twitter-egg-icon.jpg",
    };
  },
  props: ["id", "comment", "name", "dateCreated", "stars", "images"],
  emits: ["suksesHapus"],
  methods: {
    formatTgl(tgl) {
      return new Date(tgl).toLocaleDateString("id-id", {
        weekday: "long",
        day: "numeric",
        month: "long",
        year: "numeric",
        hour: "2-digit",
        minute: "2-digit",
      });
    },
    hapusReview(event) {
      let r = confirm("Yakin mau hapus?");
      if (r) {
        let details = event.target.parentElement.parentElement;
        fetch(`https://review-backend.herokuapp.com/api/v1/review/${this.id}`, {
          method: "delete",
        })
          .then((response) => response.json())
          .then((result) => {
            details.open = false;
            this.$emit("suksesHapus", result);
          })
          .catch((error) => {
            window.alert(error);
            this.isLoading = false;
          });
      }
    },
    ubahReview(event) {
      let details = event.target.parentElement.parentElement;
      details.open = false;
      this.isEditMode = true;
    },
    fixUbahReview() {
      let r = confirm("Yakin mau simpan perubahan?");
      if (r) {
        this.isLoading = true;
        let formdata = new FormData();
        // let unggahan = event.target.querySelector("#unggah-file");
        formdata.append("name", this.name_toBeEdited);
        formdata.append("review_comment", this.comment_toBeEdited);
        formdata.append("review_star", this.stars_toBeEdited);
        // unggahan.files.forEach((item) => {
        //   formdata.append("images", item, item.name);
        // });
        fetch(`https://review-backend.herokuapp.com/api/v1/review/${this.id}`, {
          method: "PATCH",
          body: formdata,
        })
          .then((response) => response.json())
          .then((result) => {
            this.isLoading = false;
            this.isEditMode = false;
            this.name_toBeShown = this.name_toBeEdited;
            this.comment_toBeShown = this.comment_toBeEdited;
            this.stars_toBeShown = this.stars_toBeEdited;
          })
          .catch((error) => {
            window.alert(error);
            this.isLoading = false;
          });
      }
    },
    batalUbahReview() {
      this.isEditMode = false;
    },
    setStars(s) {
      this.stars_toBeEdited = s;
    },
  },
  components: {
    StarsFilled,
    StarsHollow,
    ThreeDots,
    DisplayPic,
    Rating,
  },
};
</script>

<style>
.image-size {
  width: 60px;
  height: 60px;
}

@media (min-width: 310px) {
  .image-size {
    width: 80px;
    height: 80px;
  }
}

@media (min-width: 576px) {
  .image-size {
    width: 150px;
    height: 150px;
  }
}

@media (max-width: 576px) {
  .card-text-size {
    font-size: 0.9rem;
  }
}

/* Dropdown without CSS by garetmckinley from Codepen */
.dropdown {
  position: relative;
}

.dropdown summary {
  list-style: none;
  list-style-type: none;
}

.dropdown div {
  position: absolute;
  width: 180px;
  right: 0;
  margin: 0;
  z-index: 2;
  text-align: center;
  list-style: none;
  border-radius: 20px 0 20px 20px;
}

.dropdown div a {
  width: 100%;
}

/* Close the dropdown with outside clicks */
.dropdown > summary::before {
  display: none;
}

.dropdown[open] > summary::before {
  content: " ";
  display: block;
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  z-index: 1;
}
</style>