<template>
  <div style="display: flex; max-width: 100vw">
    <div class="popover"
         :class="{shown: isShown}"
         :style="{ left: pos.x+'px', top: pos.y +'px'}">
      Hi I am a popover;
    </div>
    <table class="fixed-table" v-once>
      <thead>
        <tr>
          <th>User Names </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(name, index) in names"
            :key="index">
          <td>{{name}}</td>
        </tr>
      </tbody>
    </table>
    <div class="unfixed-table"
         @scroll="debouncedScroll">
      <table>
        <thead>
          <th v-for="(name, i) in names"
              :key="i" v-once>
            {{name}}
          </th>
        </thead>
        <tbody @click="onClick">
          <tr v-for="(name, index) in names"
              :key="index" v-once>
            <td v-for="(name2, index2) in names"
                :id="`${index2}-${name2}-${name}`"
                :key="index2"
                :style="{background: getColor()}"
                class="cell" v-once></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import names from '../names';
import colors from '../colors';
import debounce from 'lodash.debounce';
export default {
  name: 'Table',
  data() {
    return {
      isShown: false,
      pos: { x: 0, y: 0, diffX: 0, diffY: 0 },
    }
  },
  created() {
    this.names = names;
    this.clickID = null;
    this.debouncedScroll = debounce((e) => {
      console.log(e);
      if (this.clickID) {
        const el = document.getElementById(this.clickID);
        if (el) {
          const { x, y } = el.getBoundingClientRect();
          if (x < 0 || y < 0) {
            this.isShown = false;
          } else if (x > 0 && y > 0) {
            this.pos = {
              ...this.pos,
              x: x + this.pos.diffX,
              y: y + this.pos.diffY,
            }
            if (!this.isShown) {
              this.isShown = true;
            }
          }
        }
      }
    }, 1000);
  },
  methods: {
    getColor() {
      const random = Math.floor(Math.random() * colors.length - 1);
      return colors[random];
    },
    onClick(e) {
      console.log(e);
      const id = e.target.id;
      if (this.isShown && this.clickID === id) {
        this.isShown = false;
        this.clickID = null;
      } else if (e.target.classList.contains('cell')) {
        const x = e.pageX;
        const y = e.pageY;
        const targetPos = e.target.getBoundingClientRect();
        const diffX = x - targetPos.x;
        const diffY = y - targetPos.y;
        this.pos = { x, y, diffX, diffY };
        this.isShown = true;
        this.clickID = id;
      } else {
        this.isShown = false;
        this.clickID = null;
      }
    }
  }
}
</script>
<style lang="scss" scoped>
.popover {
  display: none;
  position: absolute;
  background-color: #61dafb;
  left: 200px;
  padding: 10px;
  border-radius: 4px;
  box-shadow: 1px 1px 2px 0px #487a3e;
  transform: translate(-8px, -50px);
  z-index: 2;

  &::after {
    content: ' ';
    position: absolute;
    border-top: 11px solid #222;
    border-left: 8px solid transparent;
    border-right: 8px solid transparent;
    bottom: -10px;
    left: 3px;
    z-index: -1;
  }

  &.shown {
    display: block;
  }
}

table {
  border-collapse: collapse;
}

table,
th,
tr,
td {
  border: 2px solid darkred;
}

td,
th {
  font-size: 10px;
  min-width: 100px;
  max-width: 100px;
  height: 50px;
}

.cell {
  width: 100px;
  height: 50px;
  background-color: #d6d6d6;
}

.fixed-table {
  position: absolute;
  left: 0;
  z-index: 1;
  background-color: white;
}

.unfixed-table {
  margin: 0 100px;
  overflow: scroll;
}
</style>
