<template>
  <div>
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
  </div>
</template>

<script>

  export default {
    props: {
      typeApartmens: {
        type: Array,
        default: true
      }
    },
    data() {
      return {
        modalApartment: false,
        choiseApartment: [],
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
      this.$emit('valueInput', this.choiseApartment);
      this.viemModalApartment();
    },
    removeChoisApartment() {
      this.choiseApartment = [];
      this.$emit('valueInput', this.choiseApartment);
    },
  }
}
</script>

<style scoped>
  .apartment {
    display: block;
    width: 296px;
    height: 40px;
    padding: 12px 10px;
    border: 1px solid #d2d2d2;
    border-radius: 8px;
    background-color: #fff;
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