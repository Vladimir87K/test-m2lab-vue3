<template>
  <div class="app">
    <h1 class="app__title">Тестовая работа M2LAB с использованием Vue3</h1>

    <div class="container">
      <RealEstateTypes 
        :typeApartmens="typeApartmens"
      />
      <RealEstateRooms
        :typeRooms="typeRooms"
      />
    </div>
  </div>
</template>

<script>
import RealEstateTypes from './components/RealEstateTypes.vue';
import RealEstateRooms from './components/RealEstateRooms.vue';
import axios from 'axios';

export default {
  components: {
    RealEstateTypes,
    RealEstateRooms
  },

  data() {
    return {
      typeApartmens: [],
      typeRooms: [],
    }
  },

  methods: {
    
    async fetchFilter() {
      try {
        const response = await axios.get('http://widget-server.m2lab.ru/getVillage/4985be39-cbea-4393-beef-2115868487ef');
        // console.log(response.data[0].rooms.split(','));
        this.typeApartmens = response.data[0].realEstateTypes.split(',');
        this.typeRooms = response.data[0].rooms.split(',');
      } catch (e) {
        console.log('Ошибочка вышла');
      }
    }
  },
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
  }
</style>
