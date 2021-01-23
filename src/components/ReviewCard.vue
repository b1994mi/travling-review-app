<template>
  <div class="card flex-row bg-light mb-2 p-3 rounded shadow-sm card-text-size">
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
          <p class="fw-light m-0">{{ formatTgl(dateCreated) }}</p>
          <p v-if="dateCreated !== updatedAt_toBeShown" class="fw-light m-0">
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
            class="btn btn-secondary btn-responsive ms-sm-2"
          >
            Batal
          </button>
          <button
            @click="fixUbahReview"
            :disabled="isLoading"
            class="btn btn-outline-danger btn-responsive ms-2"
          >
            Simpan
          </button>
        </image-input>
      </div>
      <div v-else class="d-flex flex-column">
        <p class="isi-review m-0 mb-1 text-break">{{ comment_toBeShown }}</p>
        <div v-if="images_toBeShown.length > 0" class="d-flex flex-wrap">
          <div
            class="overflow-hidden mt-2 me-3 rounded-3 d-flex align-items-center image-size"
            v-for="image in images_toBeShown"
            :key="image"
          >
            <img
              class="w-100"
              :src="urlizer(image)"
              :alt="image.originalName"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DisplayPic from "./DisplayPic";
import ImageInput from "./ImageInput";
import Rating from "./Rating";
import StarsFilled from "./StarsFilled";
import StarsHollow from "./StarsHollow";
import ThreeDots from "./ThreeDots";
export default {
  components: {
    StarsFilled,
    StarsHollow,
    ThreeDots,
    DisplayPic,
    Rating,
    ImageInput,
  },
  props: [
    "id",
    "comment",
    "name",
    "dateCreated",
    "updatedAt",
    "stars",
    "images",
  ],
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
      images_toBeEdited: this.imagesToBlobs(this.images),
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
    hapusReview(event) {
      let r = confirm("Yakin mau hapus?");
      if (r) {
        let details = event.target.parentElement.parentElement;
        fetch(`http://localhost:5050/api/v1/review/${this.id}`, {
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
      if (!this.name_toBeEdited) {
        return alert("Mohon isi nama Anda.");
      }
      if (!this.comment_toBeEdited) {
        return alert("Mohon isi ulasan Anda.");
      }
      if (!this.stars_toBeEdited) {
        return alert("Mohon isi jumlah bintang Anda.");
      }
      let r = confirm("Yakin mau simpan perubahan?");
      if (r) {
        this.isLoading = true;
        let formdata = new FormData();
        formdata.append("name", this.name_toBeEdited);
        formdata.append("review_comment", this.comment_toBeEdited);
        formdata.append("review_star", this.stars_toBeEdited);
        this.images_toBeEdited.forEach((item) => {
          formdata.append("images", item, item.name);
        });
        fetch(`http://localhost:5050/api/v1/review/${this.id}`, {
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
            this.images_toBeShown = this.images_toBeEdited;
            this.updatedAt_toBeShown = new Date();
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
    setImgs(arr) {
      this.images_toBeEdited = arr;
    },
    urlizer(img) {
      return img instanceof Blob
        ? URL.createObjectURL(img)
        : "data:image/jpeg;base64," + img.b64;
    },
    b64toBlob(b64Data, contentType = "", sliceSize = 512) {
      let byteCharacters = atob(b64Data);
      let byteArrays = [];
      let bcl = byteCharacters.length;
      for (let offset = 0; offset < bcl; offset += sliceSize) {
        let slice = byteCharacters.slice(offset, offset + sliceSize);
        let byteNumbers = new Array(slice.length);
        for (let i = 0; i < slice.length; i++) {
          byteNumbers[i] = slice.charCodeAt(i);
        }
        let byteArray = new Uint8Array(byteNumbers);
        byteArrays.push(byteArray);
      }
      let blob = new Blob(byteArrays, { type: contentType });
      return blob;
    },
    imagesToBlobs(images = []) {
      return images.map((image) => {
        let theBlob = this.b64toBlob(image.b64);
        theBlob.lastModifiedDate = new Date();
        theBlob.name = image.originalName;
        return theBlob;
      });
    },
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