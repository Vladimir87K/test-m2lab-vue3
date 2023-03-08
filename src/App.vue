<template>
  <div class="app">
    <h1 class="app__title">Тестовая работа M2LAB с использованием Vue3</h1>

    <div class="container">
      <RealEstateTypes 
        @valueInput="showNewUrl"
        :typeApartmens="typeApartmens"
      />

      <Price
        :dataPrice="dataPrice"
      />
      
      <RealEstateRooms
        @valueInput="showNewUrl"
        :typeRooms="typeRooms"
      />
    </div>
  </div>
</template>

<script>
import RealEstateTypes from './components/RealEstateTypes.vue';
import RealEstateRooms from './components/RealEstateRooms.vue';
import Price from './components/Price.vue';
import axios from 'axios';

export default {
  components: {
    RealEstateTypes,
    Price,
    RealEstateRooms
  },

  data() {
    return {
      typeApartmens: [],
      typeRooms: [],
      dataPrice: {
        minPrice: 1,
        maxPrice: 100,
        progressMinPrice: 1,
        progressMaxPrice: 100,
      }
    }
  },
  methods: {
    async fetchFilter() {
      try {
        const response = await axios.get('http://widget-server.m2lab.ru/getVillage/4985be39-cbea-4393-beef-2115868487ef');
        console.log(response.data[0].price);
        this.typeApartmens = response.data[0].realEstateTypes.split(',');
        this.typeRooms = response.data[0].rooms.split(',');
        [this.dataPrice.minPrice, this.dataPrice.maxPrice] = response.data[0].price.split(',');
        [this.dataPrice.progressMinPrice, this.dataPrice.progressMaxPrice] = response.data[0].price.split(',');
      } catch (e) {
        console.log('Ошибочка вышла');
      }
    },
    showNewUrl(e) {
      console.log(e)
    },
    showUrl(e) {
      console.log(e)
      // const newUrl = new URL(`${minPrice}&minPrice=${maxPrice}&maxPrice`, url);
    }
  },
  // watch: {
  //   dataPrice: {
  //     handler(newValue) {
  //       console.log(newValue.progressMaxPrice)
  //     },
  //     deep: true
  //   },
  // },
  mounted() {
    this.fetchFilter();
  }
}
</script>

<style>
  * {
    box-sizing: border-box;
    font-size: 15px;
    line-height: 22px;
    font-weight: 400;
    margin: 0;
    padding: 0;
  }

  .app {
    padding: 20px;
    height: 100vh;
  } 

  .container {
    display: flex;
    justify-content: space-between;
    position: relative;
    width: 1100px;
  }

  
</style>
