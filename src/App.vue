<template>
  <div class="app">
    <h1 class="app__title">Тестовая работа M2LAB с использованием Vue3</h1>

    <div class="container">
      <button 
        class="apartment" 
        @click="viemModalApartment"
        >
        {{ choiseApartment.length > 0 ? choiseApartment.join(', ') : 'Тип недвижимости'}}
        <span 
          @click.stop="removeChoisApartment"
          class="remove"
          :class="{remove_action: choiseApartment.length > 0}"
        >
        +
        </span>
        <span 
          class="check"
          :class="{check_action: modalApartment}"
            >
         </span>
      </button>
        <div  class="apartment__modal" :class="{active: modalApartment}">
          <p 
            @click="checkApartment"
            class="apartment__modal-point"
            v-for="typeApartmen in typeApartmens"
            >
            {{ typeApartmen }}
          </p>
        </div>
      <div class="room">
        <button class="room__button" type="button">Комнатность<span></span><span></span></button>
        <div class="room__modal">

        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      typeApartmens: [],
      typeRooms: [],
      choiseApartment: [],
      modalApartment: false,
    }
  },
  methods: {
    viemModalApartment() {
      this.modalApartment = !this.modalApartment;
    },
    checkApartment(e) {
      const apartment = e.target.outerText;
      if (!this.choiseApartment.find((i) => i === apartment)) {
        this.choiseApartment.push(e.target.outerText)
      } else {
        this.choiseApartment = this.choiseApartment.filter((i) => i !== apartment)
      }
      this.viemModalApartment()
    },
    removeChoisApartment() {
      this.choiseApartment = [];
    },

    async fetchFilter() {
      try {
        const response = await axios.get('http://widget-server.m2lab.ru/getVillage/4985be39-cbea-4393-beef-2115868487ef');
        console.log(response.data[0].realEstateTypes.split(','));
        this.typeApartmens = response.data[0].realEstateTypes.split(',')
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
  } 

  .container {
    display: flex;
    justify-content: space-between;
    position: relative;
  }

  .apartment {
    display: block;
    width: 296px;
    height: 40px;
    padding: 12px 10px;
    border: 1px solid #d2d2d2;
    border-radius: 8px;
    background-color: #fff;
    text-rendering: optimizeSpeed;
    display: flex;
    align-items: center;
    position: relative;
  }

  .check {
    display: block;
    width: 8px;
    height: 8px;
    border-bottom: 1px solid #69B574;
    border-left: 1px solid #69B574;
    transform: rotate(-45deg);
    position: absolute;
    right: 10px;
    top: 13px;
    transition: all .5s ease;
  }

  .check_action {
    transform: rotate(-225deg);
    top: 15px
  }

  .remove {
    display: none;
    color: #8F8F8F;
    transform: rotate(45deg);
    position: absolute;
    top: 8px;
    right: 35px;
  }

  .remove_action {
    display: block;
  }

  .apartment:hover {
    cursor: pointer;
  }

  .apartment__modal {
    display: none;
    position: absolute;
    top: 55px;
    left: 0;
    box-shadow: 0 2px 10px 0 rgba(40, 40, 40, 0.18);
    border-radius: 10px;
  }

  .active {
    display: block;
  }

  .apartment__modal-point {
    padding: 10px 20px;
    width: 296px;
  }

  .apartment__modal-point:hover {
    background-color: #f5f5f5;
  }
</style>
