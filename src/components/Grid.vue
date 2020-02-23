<template>
  <div
    class="wrapper"
    @mousedown.prevent="mouseDown"
    @mouseup="mouseUp"
    @mousemove.prevent="mouseMove"
  >
    <div class="grid">
      <div v-for="(line, index) in matrix" :key="index"  class="line">
        <div v-for="(value, key) in line" :key="key" :data-line="index" :data-index="key" class="cell">
          <div class="cell-wrapper" :class="{ 'active': value }">
            <img v-if="value" src="../assets/check.png"/>
            <div v-else class="cross-wrapper">
              <img src="../assets/cross.png"/>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="helper" ref="helper"/>
    <div class="helper" ref="highlighter"/>
    <Modal v-if="isModalOpen" :data="modalArray" :matrix="matrix" @edit="edit" @close="isModalOpen = false"/>
  </div>
</template>

<script>
import Modal from './Modal.vue'


const matrix = [
  [1, 0, 1, 0, 1],
  [1, 0, 0, 0, 1],
  [1, 1, 0, 0, 1],
  [0, 0, 1, 0, 1],
  [0, 0, 0, 0, 1]
]
const startCoords = { x: 0, y: 0 }


export default {
  name: 'Grid',
  data() {
    return {
      matrix,
      choosedCells: [],
      modalArray: [],
      isModalOpen: false,
    }
  },

  methods: {
    edit: function (newMatrix) {
      this.matrix = newMatrix
    },
    mouseDown: function(e) {
      startCoords.x = e.pageX
      startCoords.y = e.pageY

      this.$refs.helper.style.left = e.pageX + 'px'
      this.$refs.helper.style.top = e.pageY + 'px'
      this.$refs.helper.style.display = 'block'
      this.$refs.helper.style.width = '0px'
      this.$refs.helper.style.height = '0px'
    },
    mouseUp: function() {
      this.$refs.helper.style.display = 'none'
      this.$refs.highlighter.style.display = 'none'
      this.modalArray = [...this.choosedCells]
      if (this.modalArray.length) {
        this.isModalOpen = true
      }
      this.choosedCells = []
    },
    mouseMove: function(e) {
      const diffX = e.pageX - startCoords.x
      const diffY = e.pageY - startCoords.y

      if (diffX >= 0) {
        this.$refs.helper.style.right = 'auto'
        this.$refs.helper.style.left = startCoords.x + 'px'
      } else {
        this.$refs.helper.style.left = 'auto'
        this.$refs.helper.style.right = document.documentElement.scrollWidth - startCoords.x + 'px'
      }

      if (diffY >= 0) {
        this.$refs.helper.style.bottom = 'auto'
        this.$refs.helper.style.top  = startCoords.y + 'px'
      } else {
        this.$refs.helper.style.top = 'auto'
        this.$refs.helper.style.bottom = document.documentElement.clientHeight - startCoords.y + 'px'
      }

      this.$refs.helper.style.width = Math.abs(diffX) + 'px'
      this.$refs.helper.style.height = Math.abs(diffY) + 'px'

      const helperCoords = this.$refs.helper.getBoundingClientRect()
      helperCoords.x += pageXOffset
      helperCoords.y += pageYOffset

      const cells = Array.from(document.getElementsByClassName('cell')).map(cell => {
        cell.position = {
          line: cell.getAttribute('data-line'),
          index: cell.getAttribute('data-index')
        } 
        return cell
      })
      this.choosedCells = cells.filter(cell => {
        const centerCoords = {
          x: cell.offsetLeft + cell.offsetWidth / 2,
          y: cell.offsetTop + cell.offsetHeight / 2,
        }
        return (helperCoords.x <= centerCoords.x) && (helperCoords.y <= centerCoords.y) && (helperCoords.x + helperCoords.width >= centerCoords.x) && (helperCoords.y + helperCoords.height >= centerCoords.y)
      })
      if (this.choosedCells.length) {
        const firstCell = this.choosedCells[0]
        const lastCell = this.choosedCells[this.choosedCells.length - 1]
        this.$refs.highlighter.style.display = 'block'
        this.$refs.highlighter.style.top = firstCell.offsetTop + 'px'
        this.$refs.highlighter.style.left = firstCell.offsetLeft  + 'px'
        this.$refs.highlighter.style.height = lastCell.offsetTop + lastCell.offsetHeight - firstCell.offsetTop + 'px'
        this.$refs.highlighter.style.width = lastCell.offsetLeft + lastCell.offsetWidth - firstCell.offsetLeft + 'px'
      } else {
        this.$refs.highlighter.style.display = 'none'
      }
    }
  },
  components: { 
    Modal
  }
}
</script>

<style lang="scss" scoped>
.wrapper {
  padding-top: 100px;
  display: flex;
  justify-content: center;
  .grid {
    background: #F3F3F3;
    padding: 30px;
    height: auto;
    .line {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      .cell {
        width: 80px;
        height: 80px;
        padding: 10px;
        .cell-wrapper {
          padding: 10px;
          border-radius: 30px;
          background: #FFAEAE;
          img {
            max-width: 100%;
            max-height: 100%;
            width: auto;
          }
          .cross-wrapper {
            padding: 15px;
          }
          &.active {
            background: #92F795;
          }
        }
      }
    }
  }
  .helper {
    position: absolute;
    display: none;
    background: #52BBFF;
    opacity: 0.5;
    width: 100px;
    height: 100px;
    top: 100px;
    left: 100px;
    bottom: auto;
    right: auto;
    border: 1px solid blue;
  }
}
</style>
