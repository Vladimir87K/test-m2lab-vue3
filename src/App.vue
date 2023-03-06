<template>
  <div class="app">
    <h1 class="app__title">Тестовая работа M2LAB с использованием Vue3</h1>

    <div class="container">
      <RealEstateTypes 
        :typeApartmens="typeApartmens"
      />

      <div class="price">
    <div class="price-title">&#x20bd;</div>
    <div class="price__min">
      <span class="price__min-subtitle">от</span>
      <input
        @change="showPrice" 
        class="price__min-value" 
        type="number" 
        :min="minPrice" 
        :max="maxPrice" 
        :value="choiceMinPrice"
        >
    </div>
    <div class="price__max">
      <span class="price__max-subtitle">до</span>
      <input 
        @change="showPrice" 
        class="price__max-value" 
        type="number" 
        :min="minPrice" 
        :max="maxPrice" 
        :value="choiceMaxPrice"
        >
    </div>
    <div class="price__progress">
        <div 
          class="price__progress-content"
          :style="{left: `${(choiceMinPrice / maxPrice) * 100}%`, right: `${100 - (this.choiceMaxPrice / this.maxPrice) * 100}%`}"
         >
        </div>
        <input 
          @input="changePrice" 
          class="price__progress-min" 
          type="range" 
          :min="minPrice" 
          :max="maxPrice" 
          :value="choiceMinPrice"
        >
        <input 
          @input="changePrice" 
          class="price__progress-max" 
          type="range" 
          :min="minPrice" 
          :max="maxPrice" 
          :value="choiceMaxPrice"
          >      
    </div>
  </div>

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
      minPrice: 1,
      maxPrice: 100,
      choiceMinPrice: 1,
      choiceMaxPrice: 100,
      step: 100000,
    }
  },

  methods: {
    changePrice(e) {
      if (this.choiceMaxPrice - this.choiceMinPrice < this.step) {
        if (e.target.classList.contains('price__progress-min')) {
          this.choiceMinPrice = this.choiceMaxPrice - this.step
        } else {
          this.choiceMaxPrice = this.choiceMinPrice + this.step
        }
      } else {
        if (e.target.classList.contains('price__progress-min')) {
          this.choiceMinPrice = e.target.value;
        } else {
          this.choiceMaxPrice = e.target.value;
        }
      }
    },
    showPrice(e) {
      const input = e.target
      if (input.classList.contains('price__min-value')) {
        if (input.value > this.choiceMaxPrice) {
          alert ('Вы ввели значение, превышающее выбранное максимальное');
          this.choiceMinPrice = this.minPrice;
          input.value = this.minPrice;
        } else if (input.value < this.minPrice) {
          alert ('Вы ввели значение меньше минимально возможного');
          this.choiceMinPrice = this.minPrice;
          input.value = this.minPrice;
        } else {
          this.choiceMinPrice = input.value;
        }
      } else {
        if (input.value < this.choiceMinPrice ) {
          alert ('Вы ввели значение, которое меньше выбранного минимального');
          this.choiceMaxPrice = this.maxPrice;
          input.value = this.maxPrice;
        } else if (input.value > this.maxPrice) {
          alert ('Вы ввели значение, превышающее максимально возможное');
          this.choiceMaxPrice = this.maxPrice;
          input.value = this.maxPrice;
        } else {
          this.choiceMaxPrice = input.value;
        }
      }
    },
    async fetchFilter() {
      try {
        const response = await axios.get('http://widget-server.m2lab.ru/getVillage/4985be39-cbea-4393-beef-2115868487ef');
        console.log(response.data[0].price);
        this.typeApartmens = response.data[0].realEstateTypes.split(',');
        this.typeRooms = response.data[0].rooms.split(',');
        [this.minPrice, this.maxPrice] = response.data[0].price.split(',');
        [this.choiceMinPrice, this.choiceMaxPrice] = response.data[0].price.split(',');
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
    width: 1100px;
  }

  .price {
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 296px;
  height: 40px;
  background-color: #fff;
  border-radius: 10px;
  border: 1px solid #D2D2D2;
  font-size: 15px;
  font-weight: 400px;
  line-height: 23px;
}

.price-title {
  width: 34px;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;  
  border-right: 1px solid #d2d2d2;
  position: absolute;
  left: 0;
}

.price__min {
  width: 128px;
  margin-left: 34px;
  position: relative;
}

.price__min,
.price__max {
  display: flex;
  flex-wrap: nowrap;
}

.price__min-subtitle, 
.price__max-subtitle {
  color: rgba(40, 40, 40, 0.5);
  margin: 0 8px 0 12px;
}

.price__min-value, 
.price__max-value {
  border: none;
  text-align: center;
  max-width: 95px;
}

.price__min-value:focus-visible, 
.price__max-value:focus-visible {
  outline: none;
  font-weight: 700;
}

.price__min::after {
  content: '';
  display: block;
  width: 1px;
  height: 24px;
  border:1px solid #d2d2d2;
  position: absolute;
  right: 0;
  top: 0;
  box-sizing: border-box;
}

.price__min-value::-webkit-outer-spin-button,
.price__min-value::-webkit-inner-spin-button,
.price__max-value::-webkit-outer-spin-button,
.price__max-value::-webkit-inner-spin-button {
  -webkit-appearance: none
}

.price__min-value:focus-visible {
  border: none;
}

.price__progress {
  display: flex;
  position: absolute;
  width: 263px;
  bottom: -1px;
  left: 34px;
  background: red;
}

.price__progress-content {
  position: absolute;
  left: 0%;
  right: 0%;
  height: 2px;
  background: #3DA14B;
  border-radius: 50px;
  top: -1px;
  max-width: 96%;
}

.price__progress-min, 
.price__progress-max {
  position: absolute;
  top: -11px;
  left: -5px;
  width: 101%;
  -webkit-appearance: none;
  pointer-events: none;
  background: none;
  outline: none;
}

.price__progress-min::-webkit-slider-thumb, 
.price__progress-max::-webkit-slider-thumb {
  pointer-events: auto;
  -webkit-appearance: none;
  width: 17px;
  height: 17px;
  background: #3DA14B;
  border-radius: 50%;
}
</style>
