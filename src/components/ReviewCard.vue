<template>
  <div class="card bg-light mb-2 p-3 rounded shadow-sm card-text-size">
    <skeleton-card v-if="id === 0 && stars === 0 && images.length === 0" />
    <div v-else class="d-flex flex-row">
      <display-pic :picURL="picURL" :picName="picName" />
      <div class="d-flex flex-wrap w-100">
        <div class="d-flex justify-content-between w-100">
          <div class="d-flex flex-column w-100">
            <div v-if="isEditMode" class="d-flex flex-wrap">
              <input
                type="text"
                class="flex-grow-1"
                placeholder="Your Name..."
                v-model="name_toBeEdited"
                :disabled="isLoading"
              />
            </div>
            <p v-else class="fw-bold m-0">{{ name_toBeShown }}</p>
            <p class="fw-light m-0">{{ formatTgl(createdAt) }}</p>
            <p v-if="createdAt !== updatedAt_toBeShown" class="fw-light m-0">
              Edited: {{ formatTgl(updatedAt_toBeShown) }}
            </p>
            <div v-if="isEditMode" class="my-2">
              <rating
                :grade="stars_toBeEdited"
                :maxStars="5"
                :hasCounter="false"
                @update="setStars"
              />
            </div>
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
        <div v-if="isEditMode" class="w-100">
          <textarea
            v-model="comment_toBeEdited"
            class="form-control my-2 align-self-stretch"
            style="font-size: inherit !important; min-height: 8em"
            placeholder="Your review here..."
            :disabled="isLoading"
          ></textarea>
          <image-input :images="images_toBeEdited" @listImgChanges="setImgs">
            <button
              @click="batalUbahReview"
              :disabled="isLoading"
              class="btn btn-primary btn-responsive ms-sm-2"
            >
              Cancel
            </button>
            <button
              @click="fixUbahReview"
              :disabled="isLoading"
              class="btn btn-outline-danger btn-responsive ms-2"
            >
              Save
            </button>
          </image-input>
        </div>
        <div v-else class="d-flex flex-column">
          <p class="isi-review m-0 mb-1 text-break">{{ comment_toBeShown }}</p>
          <div v-if="images_toBeShown.length > 0" class="d-flex flex-wrap">
            <div
              :style="'background-image: url(' + urlizer(image) + ')'"
              class="mt-2 me-3 rounded-3 image-size"
              v-for="image in images_toBeShown"
              :key="image"
            ></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * TODO:
 * 🗸 1. hapus function yang tidak digunakan
 * 2. potong2 fix ubah review menjadi function2 yang lbh mudah dibaca
 * 🗸 3. buat form data ada pergantian di rating star supaya ada terpicu utk ubah updatedAt
 * 🗸 4. hapusReview() dibuat async aja
 * 🗸 5. deploy pake teknik gh-pages aja
 * 6. pakai plugin sejenis sweet alert tp lbh ringan
 * 🗸 7. puter if(!r) lempar execption aja spy indentasi berkurang
 * 🗸 8. img thumbnail harus bisa full kotak walau landscape ratio
 * 9. urlizer(img) dibuat jadi mixin saja, dan coba cari func lain yg bisa dijdkan mixin
 * 10. ketika POST awal di reviewform dan reviwapp, ada loading ketika akan push card
 * 11. gambar di card bisa di-click seperti twitter/ review di shopee
 */
import DisplayPic from "./DisplayPic";
import ImageInput from "./ImageInput";
import Rating from "./Rating";
import StarsFilled from "./StarsFilled";
import StarsHollow from "./StarsHollow";
import ThreeDots from "./ThreeDots";
import SkeletonCard from "./SkeletonCard";
import { mainFetchURL } from "@/const.js";
export default {
  components: {
    StarsFilled,
    StarsHollow,
    ThreeDots,
    DisplayPic,
    Rating,
    ImageInput,
    SkeletonCard,
  },
  props: ["id", "comment", "name", "createdAt", "updatedAt", "stars", "images"],
  emits: ["suksesHapus"],
  data() {
    return {
      isEditMode: false,
      isLoading: false,
      name_toBeShown: this.name,
      comment_toBeShown: this.comment,
      stars_toBeShown: this.stars,
      images_toBeShown: this.images,
      updatedAt_toBeShown: this.updatedAt,
      name_toBeEdited: this.name,
      comment_toBeEdited: this.comment,
      stars_toBeEdited: this.stars,
      images_toBeEdited: this.images,
      picURL:
        "https://abs.twimg.com/sticky/default_profile_images/default_profile_400x400.png", // Suatu saat ini bisa diganti prop
      picName: "twitter-egg-icon.jpg",
    };
  },
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
    async hapusReview(event) {
      let r = confirm("Yakin mau hapus?");
      if (!r) return;
      let details = event.target.parentElement.parentElement;
      try {
        await fetch(`${mainFetchURL}/${this.id}`, {
          method: "delete",
        });
        details.open = false;
        this.$emit("suksesHapus", this.id);
      } catch (error) {
        window.alert(error);
        this.isLoading = false;
      }
    },
    ubahReview(event) {
      let details = event.target.parentElement.parentElement;
      details.open = false;
      this.isEditMode = true;
    },
    async fixUbahReview() {
      if (!this.name_toBeEdited) return alert("Mohon isi nama Anda.");
      if (!this.comment_toBeEdited) return alert("Mohon isi ulasan Anda.");
      if (!this.stars_toBeEdited) return alert("Isi jumlah bintang Anda.");
      let r = confirm("Yakin mau simpan perubahan?");
      if (!r) return;
      this.isLoading = true;
      let formdata = new FormData();
      this.name_toBeEdited !== this.name_toBeShown &&
        formdata.append("name", this.name_toBeEdited);
      this.comment_toBeEdited !== this.comment_toBeShown &&
        formdata.append("review_comment", this.comment_toBeEdited);
      formdata.append("review_star", this.stars_toBeEdited);
      this.images_toBeEdited.forEach((el) => {
        const inc = this.images_toBeShown.includes(el);
        // Kalo ada false, pasti itu tambah gambar.
        if (!inc) formdata.append("images", el, el.name);
      });
      this.images_toBeShown.forEach((el) => {
        const inc = this.images_toBeEdited.includes(el);
        // Kalo ada false, pasti itu hapus gambar.
        if (!inc) formdata.append("images_toBeDeleted", el.id);
      });
      try {
        const response = await fetch(`${mainFetchURL}/${this.id}`, {
          method: "PATCH",
          body: formdata,
        });
        const { status, message, data } = await response.json();
        if (status !== 200) throw message;
        this.isLoading = this.isEditMode = false;
        this.name_toBeShown = this.name_toBeEdited;
        this.comment_toBeShown = this.comment_toBeEdited;
        this.stars_toBeShown = this.stars_toBeEdited;
        // START logic utk jk user mau upload gambar persis sama berkali2
        // Hilangkan dulu property id di data.Images jk ada di toBeShown
        for (let i = 0; i < data.Images.length; i++) {
          const elm = data.Images[i];
          for (let j = 0; j < this.images_toBeShown.length; j++) {
            const el = this.images_toBeShown[j];
            if (elm.id === el.id) {
              delete elm.id;
              break;
            }
          }
        }
        // baru kemudian cari images yang ditambah.
        this.images_toBeEdited.forEach((el) => {
          const inc = this.images_toBeShown.includes(el);
          // Kalo ada false, pasti itu tambah gambar.
          if (!inc) {
            for (let i = 0; i < data.Images.length; i++) {
              const elm = data.Images[i];
              if (elm.originalname === el.name && elm.id) {
                el.id = elm.id;
                delete elm.id;
                break;
              }
            }
          }
        });
        // END logic utk jk user mau upload gambar persis sama berkali2
        this.images_toBeShown = this.images_toBeEdited;
        this.updatedAt_toBeShown = new Date();
      } catch (error) {
        window.alert(error);
        this.isLoading = false;
      }
    },
    batalUbahReview() {
      this.isEditMode = false;
    },
    setStars(s) {
      this.stars_toBeEdited = s;
    },
    setImgs(arr) {
      this.images_toBeEdited = arr;
    },
    urlizer(img) {
      return URL.createObjectURL(img);
    },
  },
};
</script>

<style>
.image-size {
  width: 60px;
  height: 60px;
  background-position: center center;
  background-repeat: no-repeat;
  background-size: cover;
  overflow: hidden;
}

.image-size img {
  min-height: 100%;
  min-width: 100%;
  opacity: 0;
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
  PATCH .card-text-size {
    font-size: 0.9rem;
  }
  .btn-responsive {
    padding: 0.25rem 0.5rem;
    font-size: 0.875rem;
    border-radius: 0.2rem;
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