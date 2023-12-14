<template>
  <ClipLoader :size="size" v-if="isLoading" />
  <div v-else>
    <div v-for="item in category" v-if="!unvaibleProduct">
      <div class="container">
        <div class="item1">
          <img class="product-image" :src="item.image" alt="" />
        </div>
        <div class="item2">
          <h1 class="text-header">{{ item.title }}</h1>
          <div class="wrapped">
            <p>{{ item.category }}</p>
            <p>{{ item.rating.rate }}/5</p>
          </div>

          <div class="describe">
            {{ item.description }}
          </div>

          <h1></h1>
          <button
            class="button1"
            :class="{
              backgroundmens: item.category == `men's clothing`,
              backgroundwomens: item.category == `women's clothing`,
            }"
          >
            Buy now
          </button>
          <button
            class="button2"
            @click="nextProduct"
            :class="{
              bordermens: item.category == `men's clothing`,
              borderwomens: item.category == `women's clothing`,
              colorwomens: item.category == `women's clothing`,
              colormens: item.category == `men's clothing`,
            }"
          >
            Next Product
          </button>
        </div>
      </div>
    </div>
    <div class="containerunvaible" v-else-if="unvaibleProduct">
      This product is unavailable to show
      <br />
      <button class="buttonerror" @click="erorProduct">Next Product</button>
    </div>
  </div>
</template>

<style>
@import './src/assets/main.css';
</style>

<script>
import ClipLoader from 'vue-spinner/src/ClipLoader.vue';
export default {
  data() {
    return {
      index: 1,
      category: [],
      unvaibleProduct: false,
      size: '150px',
      isLoading: false,
    };
  },
  components: {
    ClipLoader,
  },
  methods: {
    // function yang di gunakan untuk get data api sesuai dengan index yang di tuju
    async getDataApi() {
      this.isLoading = true;
      let response = await fetch(`https://fakestoreapi.com/products/${this.index}`);
      let res = await response.json();
      // pengkondisian untuk push ke dalam array category apabila nilai category
      // berisi women's dan men's selain itu tidak di push ke category
      if (res.category == `women's clothing` || res.category == `men's clothing`) {
        this.category.push(res);
        this.isLoading = false;
      } else {
        // this.unvaibleProduct akan di set ke true, hal ini bertujuan agar jika category selain
        // womens dan mens tidak akan di tampilkan di layar
        this.unvaibleProduct = true;
        this.isLoading = false;
      }
    },
    // function untuk get API selanjutnya dan mengulang nya apabila sudah melebihi 20
    async nextProduct() {
      this.category = [];

      if (this.index < 20) {
        // this.index++ diperlukan untuk mendapatkan index selanjutnya
        this.index++;
        // setelah menambahkan index nya kita panggil function getDataApi
        await this.getDataApi(this.index);
        this.category.splice(1);
      } else {
        // ini adalah pengkondisian di mana kita mengembalikan index ke no 1 apabila sudah mencapai 20
        this.index = 1;
        await this.getDataApi(this.index);
      }
    },
    // ini adalah function untuk menjalankan product unvaible
    async erorProduct() {
      this.isLoading = true;
      this.index++;
      let response = await fetch(`https://fakestoreapi.com/products/${this.index}`);
      let res = await response.json();
      // pengkondisian ini untuk mengembalikan nilai unvaibleProduct agar kembali menjadi false
      if (res.category == `women's clothing` || res.category == `men's clothing`) {
        this.unvaibleProduct = false;
        this.isLoading = false;
        await this.getDataApi(this.index);
      } else {
        this.isLoading = false;
      }
    },
  },
  mounted() {
    this.getDataApi();
  },
};
</script>
