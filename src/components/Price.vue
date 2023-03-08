<template>
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
          :style="{left: `${(dataPrice.progressMinPrice / dataPrice.maxPrice) * 100}%`, right: `${100 - (dataPrice.progressMaxPrice / dataPrice.maxPrice) * 100}%`}"
         >
        </div>
        <input 
          @input="changePrice" 
          class="price__progress-min" 
          type="range" 
          :min="dataPrice.minPrice" 
          :max="dataPrice.maxPrice" 
          :value="dataPrice.progressMinPrice"
        >
        <input 
          @input="changePrice" 
          class="price__progress-max" 
          type="range" 
          :min="dataPrice.minPrice" 
          :max="dataPrice.maxPrice" 
          :value="dataPrice.progressMaxPrice"
          >      
    </div>
  </div>

</template>

<script>
  export default {
    props: {
      dataPrice: {
        type: Object,
        default: true
      }
    },
    data() {
      return {
        choiceMinPrice: '1',
        choiceMaxPrice: '100000',
        step: 1000000,
      }
    },
    watch: {
      dataPrice(newValue, oldValue) {
        console.log(newValue, oldValue)
        this._startingValue();
      }
    },
    methods: {
      changePrice(e) {
        if (this.dataPrice.progressMaxPrice - this.dataPrice.progressMinPrice < this.step) {
          if (e.target.classList.contains('price__progress-min')) {
            this.dataPrice.progressMinPrice = this.dataPrice.progressMaxPrice - this.step;
          } else {
            this.dataPrice.progressMaxPrice = this.dataPrice.progressMinPrice + this.step;
          }
        } else {
          if (e.target.classList.contains('price__progress-min')) {
            this.dataPrice.progressMinPrice = e.target.value;
          } else {
            this.dataPrice.progressMaxPrice = e.target.value;
          }
        }
      },
      showPrice(e) {
        const input = e.target;
        const value = Number(e.target.value.replace(/[^0-9]/g, ''));
        if (input.classList.contains('price__min-value')) {
          if (value > this.dataPrice.progressMaxPrice) {
            alert ('Вы ввели значение, превышающее выбранное максимальное');
            this.dataPrice.progressMinPrice = this.dataPrice.minPrice;
            input.value = this._numberWithSpaces(this.dataPrice.minPrice);
          } else if (value < this.dataPrice.minPrice) {
            alert ('Вы ввели значение меньше минимально возможного');
            this.dataPrice.progressMinPrice = this.dataPrice.minPrice;
            input.value = this._numberWithSpaces(this.dataPrice.minPrice);
          } else {
            this.dataPrice.progressMinPrice = value;
          }
        } else {
          if (value < this.dataPrice.progressMinPrice ) {
            alert ('Вы ввели значение, которое меньше выбранного минимального');
            this.dataPrice.progrressMaxPrice = this.dataPrice.maxPrice;
            input.value = this._numberWithSpaces(this.dataPrice.maxPrice);
          } else if (value > this.dataPrice.maxPrice) {
            alert ('Вы ввели значение, превышающее максимально возможное');
            this.dataPrice.progressMaxPrice = this.dataPrice.maxPrice;
            input.value = this._numberWithSpaces(this.dataPrice.maxPrice);
          } else {
            this.dataPrice.progressMaxPrice = value;
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
    },
    updated() {
      this.choiceMaxPrice = this._numberWithSpaces(this.dataPrice.progressMaxPrice);
      this.choiceMinPrice = this._numberWithSpaces(this.dataPrice.progressMinPrice);
    },
  //   watch: {
  //   dataPrice: {
  //     handler(newValue) {
  //       console.log(newValue)
  //     },
  //     deep: true
  //   },
  //   typeApartmens(newValue) {
  //     console.log(newValue)
  //   }
  // },
  }
</script>

<style scoped>
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