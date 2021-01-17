<template>
  <div class="card flex-row bg-light mb-2 p-3 rounded shadow-sm card-text-size">
    <display-pic :picURL="picURL" :picName="picName" />
    <div class="d-flex flex-wrap w-100">
      <div class="d-flex w-100">
        <div class="d-flex flex-column">
          <p class="fw-bold m-0">{{ name }}</p>
          <p class="fw-light m-0">{{ formatTgl(dateCreated) }}</p>
          <p class="m-0 mb-1">
            <stars-filled v-for="n in stars" :key="n" />
            <stars-hollow v-for="n in 5 - stars" :key="n" />
          </p>
        </div>
        <div class="d-flex flex-grow-1 justify-content-end">
          <details class="dropdown">
            <summary role="button">
              <a class="button">
                <three-dots />
              </a>
            </summary>
            <div>
              <a @click="ubahReview" class="btn btn-warning p-1">Edit review</a>
              <a @click="hapusReview" class="btn btn-danger p-1"
                >Delete review</a
              >
            </div>
          </details>
        </div>
      </div>
      <div class="d-flex flex-column">
        <p class="isi-review m-0 mb-1 text-break">{{ comment }}</p>
        <div class="d-flex flex-wrap">
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
import StarsFilled from "./StarsFilled";
import StarsHollow from "./StarsHollow";
import ThreeDots from "./ThreeDots";
export default {
  data() {
    return {
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
  },
  components: {
    StarsFilled,
    StarsHollow,
    ThreeDots,
    DisplayPic,
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
  width: 150px;
  right: 0;
  margin: 0;
  z-index: 2;
  text-align: center;
  list-style: none;
  border-radius: 20px 0 20px 20px;
  overflow: hidden;
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