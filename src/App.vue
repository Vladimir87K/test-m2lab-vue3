<template>
  <div class="app">
    <h1 class="app__title">Тестовая работа M2LAB с использованием Vue3</h1>

    <div class="container">
      <RealEstateTypes 
        @valueInput="_typesNewUrl"
        :typeApartmens="typeApartmens"
      />

      <Price
        :dataPrice="dataPrice"
      />
      
      <RealEstateRooms
        @valueInput="_roomsNewUrl"
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
      },
      _url: '',
      paramNewUrl: {
        rooms: 0,
        minPrice: 1,
        maxPrice: 100,
        realEstateTypes: '',
      },
      _newUrl: null,
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
    _roomsNewUrl(e) {
      if (e.length >= 1) {
        this.paramNewUrl.rooms = `?rooms=${e.join('&')}rooms`;
      } else {
        this.paramNewUrl.rooms = ``;
      }
    },
    _typesNewUrl(e) {
      if (e.length >= 1) {
        this.paramNewUrl.realEstateTypes = `?realEstateTypes=${e.join('&')}realEstateTypes`;
      } else {
        this.paramNewUrl.realEstateTypes = '';
      }
    },
    _showNewUrl() {
      const newUrl = new URL(`${this.paramNewUrl.rooms}${this.paramNewUrl.minPrice}${this.paramNewUrl.maxPrice}${this.paramNewUrl.realEstateTypes}`, this._url);
      window.history.replaceState(null, null, newUrl)
      console.log(window.location.search) 
    },
    // _wathNewUrl() {
    //   this._newUrl = setInterval(() => {
    //     const urlSearch = window.location.search;
    //     console.log(urlSearch)
		// }, 3000)
    // }
  },
  watch: {
    dataPrice: {
      handler(newValue) {
        if (newValue.progressMaxPrice) this.paramNewUrl.maxPrice = `?maxPrice=${newValue.progressMaxPrice}`;
        if (newValue.progressMinPrice) this.paramNewUrl.minPrice = `?minPrice=${newValue.progressMinPrice}`;
      },
      deep: true
    },
    paramNewUrl: {
      handler(newValue) {
        this._showNewUrl()
      },
      deep: true
    }
  },
  mounted() {
    this.fetchFilter();
    this._url = document.URL;
    this._wathNewUrl();
  },
  // beforeDestroy() {
  //   clearInterval(this._newUrl)
  // }
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
    margin-top: 20px;
  }

  
</style>
