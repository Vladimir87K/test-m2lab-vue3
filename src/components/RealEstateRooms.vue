<template>
  <div class="room">
    <button 
      class="room__button" 
      @click="viemModal" 
      type="button">
        {{ choiseRoom.length > 0 ? `${choiseRoom.join(', ')} комн` :'Комнатность'}}
        <span
          @click.stop="removeChoiseRoom"
          class="remove"
          :class="{remove_action: choiseRoom.length > 0}"
          >
            +
          </span>
          <span
            class="check"
            :class="{check_action: modalRoom}"
          >
          </span>
    </button>
    <div class="container-room-modal" :class="{action: modalRoom}" @click="closeModal">
      <div  class="room__modal">
        <label 
          @click="checkPoint"
          v-for="(room, index) in typeRooms"
          class="room__modal-point"
          >
            {{ room }}
            <input 
              class="room__modal_checkbox" 
              type="checkbox"
              :value="index"
            >
            <span 
              class="room__modal_checkmark"
            >
            </span>
        </label>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      typeRooms: {
        type: Array,
        default: true
      }
    },
    data() {
      return {
        modalRoom: false,
        choiseRoom: []
      }
    },
    methods: {
      viemModal() {
      this.modalRoom = !this.modalRoom
    },
    checkPoint(e) {
      if(e.target.value !== undefined) {
        const room = Number(e.target.value) + 1;
        if (!this.choiseRoom.find(i => i === room || i === 'C') ) {
          if (room !== this.typeRooms.length) {
            this.choiseRoom.push(room);
          } else {
            this.choiseRoom.push('C');
          }
        } else if (room !== undefined) {
          if (room === this.typeRooms.length) {
            this.choiseRoom = this.choiseRoom.filter(i => i !== "C");
          } else {
            this.choiseRoom = this.choiseRoom.filter(i => i !== room);
          }
        }
        e.target.parentNode.classList.toggle('room__modal-point_checked');
      }
    },
    removeChoiseRoom() {
      this.choiseRoom = [];
      const points = document.querySelectorAll('.room__modal-point');
      
      points.forEach((point) => {
        if (point.classList.contains('room__modal-point_checked')) {
          point.classList.remove('room__modal-point_checked');
          point.childNodes[1].checked = false;
        }
      })
    },
    closeModal(e) {
      if (e.target === e.currentTarget) {
        this.viemModal();
        }
      },
    }
  }
</script>

<style  scoped>
  .room__button {
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

  .container-room-modal {
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    background: rgba(0, 0, 0, 0); 
    position: fixed;
    display: none;
  }

  .room__modal {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    width: 296px;
    position: absolute;
    top: 95px;
    right: 20px;
    box-shadow: 0 2px 10px 0 rgba(40, 40, 40, 0.18);
    border-radius: 10px;
  }

  .room__modal:hover {
    cursor: pointer;
  }

  .action {
    display: block;
  }

  .room__modal-point {
    width: 100%;
    display: flex;
    flex-direction: row-reverse;
    align-items: center;
    justify-content: flex-end;
    padding: 12px 16px;  
    user-select: none;
  }

  .room__modal-point_checked {
    background-color: #f5f5f5;
  }

  .room__modal_checkbox {
    opacity: 0;
    height: 0;
    width: 0;
  }

  .room__modal_checkmark {
    display: block;
    height: 23px;
    width: 22px;
    border: 1px solid #d2d2d2;
    background-color: #FFFFFF; 
    border-radius: 2px;
    margin-right: 16px;
    position: relative;
  }

  .room__modal_checkmark:hover {
    cursor: pointer;
    background-color: #d2d2d2;
  }

  .room__modal_checkbox:checked ~ .room__modal_checkmark {
    background-color: #3DA14B;
  }

  .room__modal_checkmark:after {
    content: "";
    position: absolute;
    left: 6px;
    top: 2px;
    width: 6px;
    height: 11px;
    border: solid #fff;
    border-width: 0 2px 2px 0;
    transform: rotate(45deg);
    display: none;
  }

  .room__modal_checkbox:checked ~ .room__modal_checkmark:after {
    display: block;
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
</style>