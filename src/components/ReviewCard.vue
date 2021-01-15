<template>
  <div class="card mb-2 rounded shadow-sm card-text-size">
    <div class="card-body bg-light">
      <div class="d-flex flex-grow-1">
        <div class="d-flex flex-column">
          <div class="rounded-circle overflow-hidden me-3 avatar-size">
            <img
              src="https://socialmedia4us.files.wordpress.com/2015/11/twitter-egg-icon.jpg"
              alt="image"
              style="width: 100%; height: 100%"
            />
          </div>
        </div>
        <div class="d-flex flex-wrap w-100">
          <div class="d-flex w-100">
            <div class="d-flex flex-column">
              <p class="fw-bold m-0">{{ name }}</p>
              <p class="fw-light m-0">{{ ubahTanggal(dateCreated) }}</p>
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
                  <a class="btn btn-warning p-1">Edit review</a>
                  <a @click="hapusReview" class="btn btn-danger p-1"
                    >Delete review</a
                  >
                </div>
              </details>
            </div>
          </div>
          <div class="d-flex flex-column">
            <p class="isi-review m-0 mb-1 text-break">{{ comment }}</p>
            <div class="d-flex">
              <div class="d-flex flex-wrap">
                <div
                  class="overflow-hidden me-3 rounded d-flex align-items-center image-size"
                  v-for="image in images"
                  :key="image"
                >
                  <img
                    class="w-100"
                    :src="tambahBase64(image.b64)"
                    :alt="image.originalName"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import StarsFilled from "./StarsFilled";
import StarsHollow from "./StarsHollow";
import ThreeDots from "./ThreeDots";
export default {
  props: ["id", "comment", "name", "dateCreated", "stars", "images"],
  emits: ["suksesHapus"],
  methods: {
    ubahTanggal(tgl) {
      let tglBaru = new Date(tgl);
      let tanggal = tglBaru.getDate();
      let bulan = tglBaru.toLocaleString("id-id", { month: "long" });
      let tahun = tglBaru.getFullYear();
      let pukul = tglBaru.toTimeString().substr(0, 5);
      let kembalikan = `${tanggal} ${bulan} ${tahun}, ${pukul}`;
      return kembalikan;
    },
    tambahBase64(b64) {
      return `data:image/jpeg;base64,${b64}`;
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
  },
};
</script>

<style>
.avatar-size {
  width: 30px;
  height: 30px;
}

.image-size {
  width: 60px;
  height: 60px;
}

@media (min-width: 310px) {
  .avatar-size {
    width: 40px;
    height: 40px;
  }
  .image-size {
    width: 80px;
    height: 80px;
  }
}

@media (min-width: 576px) {
  .avatar-size {
    width: 60px;
    height: 60px;
  }
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