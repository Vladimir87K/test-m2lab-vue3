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
        @input="viemInputPrice"
        class="price__min-value" 
        type="text" 
        :value="choiceMinPrice"
        >
    </div>
    <div class="price__max">
      <span class="price__max-subtitle">до</span>
      <input 
        @change="showPrice" 
        @input="viemInputPrice"
        class="price__max-value" 
        type="text" 
        :value="choiceMaxPrice"
        >
    </div>
    <div class="price__progress">
        <div 
          class="price__progress-content"
          :style="{left: `${(progressMinPrice / maxPrice) * 100}%`, right: `${100 - (progressMaxPrice / maxPrice) * 100}%`}"
         >
        </div>
        <input 
          @input="changePrice" 
          class="price__progress-min" 
          type="range" 
          :min="minPrice" 
          :max="maxPrice" 
          :value="progressMinPrice"
        >
        <input 
          @input="changePrice" 
          class="price__progress-max" 
          type="range" 
          :min="minPrice" 
          :max="maxPrice" 
          :value="progressMaxPrice"
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
      choiceMinPrice: '1',
      choiceMaxPrice: '100000',
      progressMinPrice: 1,
      progressMaxPrice: 100,
      step: 1000000,
    }
  },

  methods: {
    changePrice(e) {
      console.log(this.progressMaxPrice - this.progressMinPrice)
      if (this.progressMaxPrice - this.progressMinPrice < this.step) {
        if (e.target.classList.contains('price__progress-min')) {
          this.progressMinPrice = this.progressMaxPrice - this.step;
          this.choiceMinPrice = this._numberWithSpaces(this.progressMinPrice)
        } else {
          this.progressMaxPrice = this.progressMinPrice + this.step;
          this.choiceMaxPrice = this._numberWithSpaces(this.progressMaxPrice)
        }
      } else {
        if (e.target.classList.contains('price__progress-min')) {
          this.choiceMinPrice =this._numberWithSpaces(e.target.value);
          this.progressMinPrice = e.target.value;
        } else {
          this.choiceMaxPrice = this._numberWithSpaces(e.target.value);
          this.progressMaxPrice = e.target.value;
        }
      }
    },
    showPrice(e) {
      // console.log(e.target)
      // console.log(e.target.value)
      // console.log(typeof(e.target.value))
      const input = e.target;
      const value = Number(e.target.value.replace(/[^0-9]/g, ''))
      if (input.classList.contains('price__min-value')) {
        if (value > this.progressMaxPrice) {
          alert ('Вы ввели значение, превышающее выбранное максимальное');
          this.progressMinPrice = this.minPrice;
          input.value = this._numberWithSpaces(this.minPrice);
        } else if (value < this.minPrice) {
          alert ('Вы ввели значение меньше минимально возможного');
          this.progressMinPrice = this.minPrice;
          input.value = this._numberWithSpaces(this.minPrice);
        } else {
          console.log('3')
          console.log(value)
          this.progressMinPrice = value;
          this.choiceMinPrice = this._numberWithSpaces(value);
        }
      } else {
        if (value < this.choiceMinPrice ) {
          alert ('Вы ввели значение, которое меньше выбранного минимального');
          this.progrressMaxPrice = this.maxPrice;
          input.value = this._numberWithSpaces(this.maxPrice);
        } else if (value > this.maxPrice) {
          alert ('Вы ввели значение, превышающее максимально возможное');
          this.progressMaxPrice = this.maxPrice;
          input.value = this._numberWithSpaces(this.maxPrice);
        } else {
          this.progressMaxPrice = value;
          this.choiceMaxPrice = this._numberWithSpaces(value);
        }
      }
    },
    viemInputPrice(e) {
      const value = e.target.value.replace(/[^0-9]/g, '');
      const newValue =  this._numberWithSpaces(value);
      e.target.value = newValue;
    },
    _numberWithSpaces(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, " ")
    },
    async fetchFilter() {
      try {
        const response = await axios.get('http://widget-server.m2lab.ru/getVillage/4985be39-cbea-4393-beef-2115868487ef');
        console.log(response.data[0].price);
        this.typeApartmens = response.data[0].realEstateTypes.split(',');
        this.typeRooms = response.data[0].rooms.split(',');
        [this.minPrice, this.maxPrice] = response.data[0].price.split(',');
        [this.progressMinPrice, this.progressMaxPrice] = response.data[0].price.split(',')
        this.choiceMinPrice = this._numberWithSpaces(this.minPrice);
        this.choiceMaxPrice = this._numberWithSpaces(this.maxPrice);
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
  width: 261px;
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
  top: -1px;
  max-width: 96%;
}

.price__progress-min, 
.price__progress-max {
  position: absolute;
  top: -9px;
  left: -5px;
  width: 101%;
  -webkit-appearance: none;
  pointer-events: none;
  background: none;
  outline: none;
}

.price__progress-max {
  width: 102%;
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
