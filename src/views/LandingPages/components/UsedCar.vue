<script setup>
import { onMounted, onUnmounted } from "vue";
import Typed from "typed.js";
import bg0 from "@/assets/img/bmw11.jpg";
import DefaultNavbar from "../../../examples/navbars/NavbarDefault.vue";
//sections
const body = document.getElementsByTagName("body")[0];
//hooks
onMounted(() => {
  body.classList.add("about-us");
  body.classList.add("bg-gray-200");

  if (document.getElementById("typed")) {
    // eslint-disable-next-line no-unused-vars
    var typed = new Typed("#typed", {
      stringsElement: "#typed-strings",
      typeSpeed: 90,
      backSpeed: 90,
      backDelay: 200,
      startDelay: 500,
      loop: true,
    });
  }
});

onUnmounted(() => {
  body.classList.remove("about-us");
  body.classList.remove("bg-gray-200");
});
</script>
<script>
import utils from "../../../utils/utils";
import api from "../../../utils/api";
export default {
  data() {
    return {
      localDomain: utils.constant.localDomain,
      usedList: [],
      video: "",
    };
  },

  async created() {
    await this.getUsedCarList();
  },
  methods: {
    async getUsedCarList() {
      const resp = await api.get("car/used");
      if (resp) {
        this.usedList = await resp.json();
        this.usedList.forEach((x) => {
          x.discountPrice = (x.price - x.price * (x.percentage / 100)).toFixed(
            2
          );
        });
      }
    },

    goToDetail(id) {
      this.$router.push({ name: "details", params: { id: id } });
    },
  },
};
</script>
<template>
  <DefaultNavbar
    :action="{
      route: 'javascript:;',
      label: 'Buy Now',
      color: 'btn-white',
    }"
    transparent
  />
  <header class="bg-gradient-dark">
    <div
      class="page-header min-vh-75"
      :style="{ backgroundImage: `url(${bg0})` }"
    >
      <span class="mask bg-gradient-dark opacity-1"></span>
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-lg-8 text-center mx-auto my-auto">
            <h2 style="color: red">USED CAR</h2>
            <h1 style="color: yellow">FOR SALE</h1>
            <p class="lead mb-4 text-white opacity-8">
              FIND THE PERFECT CAR , FOR SALE TODAY!
            </p>
          </div>
        </div>
      </div>
    </div>
  </header>
  <div class="card card-body shadow-xl mx-3 mx-md-4 mt-n6">
    <h2 class="text-black" style="text-align: center">BEST USED CAR</h2>
    <div class="container">
      <div class="row col-md-12">
        <template v-for="(used, index) in usedList" :key="index">
          <span class="col-md-6 mt-5 imgHolder">
            <img
              :src="this.localDomain + '/car' + used.image_path"
              class="carImg"
              :key="imageIndex"
            />
            <h4>{{ used.name }}</h4>
            <h6 v-if="!used.percentage">{{ used.price }} $</h6>
            <h6 v-if="used.percentage">
              <del
                v-if="used.percentage"
                style="color: #a59898; font-size: smaller"
                >{{ used.price }} $</del
              >
              <span>{{ used.discountPrice }} $</span>
            </h6>
            <span class="more" @click="goToDetail(used.id)"> Learn More </span>

            <span v-if="used.percentage" class="discountCar"
              ><span style="font-weight: 900; font-size: larger">
                {{ used.percentage }}%
              </span>
              <span style="font-size: small">discount</span></span
            >
          </span>
        </template>
      </div>
    </div>
  </div>
</template>

<style>
.rightSide {
  margin-bottom: 25%;
}
</style>
