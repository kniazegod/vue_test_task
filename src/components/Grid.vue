<template>
  <div
    class="wrapper"
    @mousedown.prevent="mouseDown"
    @mouseup="mouseUp"
    @mousemove.prevent="mouseMove"
  >
    <div class="grid">
      <div v-for="(line, index) in matrix" :key="index"  class="line">
        <div v-for="(value, key) in line" :key="key" class="cell">
          <img v-if="value" src="../assets/check.png"/>
          <div v-else class="cross-wrapper">
            <img src="../assets/cross.png"/>
          </div>
        </div>
      </div>
    </div>
    <div class="helper" ref="helper"/>
  </div>
</template>

<script>
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
    }
  },

  methods: {
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
      this.$refs.helper.style.display = 'none';
      console.log(Array.from(document.getElementsByClassName('cell')).map(cell => {
        return {
          offsetTop: cell.offsetTop,
          offsetLeft: cell.offsetLeft,
          offsetWidth: cell.offsetWidth,
          offsetHeight: cell.offsetHeight
        }
      }))
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
    }
  }
}
</script>

<style lang="scss" scoped>
.wrapper {
  padding-top: 100px;
  min-height: 100vh;
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
        width: 60px;
        height: 60px;
        margin: 10px;
        border-radius: 30px;
        padding: 10px;
        background: #92F795;
        img {
          max-width: 100%;
          max-height: 100%;
          width: auto;
        }
        .cross-wrapper {
          padding: 15px;
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
