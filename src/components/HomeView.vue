<template>
  <v-main id="main" height="100%" width="100%">
    <h1 :style="{height:titleHeight+'px', width:'100%'}">Home <span v-on:click="addHorItem()">ad</span></h1>
  <grid-layout id="gridLayout"
               :layout.sync="layout"
               :col-num="colNum"
               :max-rows="maxRows"
               :row-height="gridHeight"
               :is-draggable="draggable"
               :is-resizable="resizable"
               :vertical-compact="true"
               :use-css-transforms="true"
      :autoSize=false
      :style="{height:'gridHeight', width:'gridWith'}"
   >
     <grid-item v-for="item in layout"
                :key=item.i
                :static="item.static"
                :x="item.x"
                :y="item.y"
                :w="item.w"
                :h="item.h"
                :i="item.i"
                @move="moveEvent"
                @moved="movedEvent"
     >
         <span class="text">{{item.i}}</span>
         <span class="remove" @click="removeItem(item.i)">x</span>
     </grid-item>
   </grid-layout>
</v-main> 
</template>

<script>
import { GridLayout, GridItem } from "vue-grid-layout"

export default {
  name: 'Home',
  components: {
      GridLayout,
      GridItem
  },
  created() {
    window.addEventListener("resize", this.onResize);
  },
  destroyed() {
    window.removeEventListener("resize", this.myEventHandler);
  },
  mounted: function() {
    this.gridHeight = this.getGridHeight();

    this.initMainElement();
  },
  methods : {
    onResize(/*e*/) {
      this.gridHeight = this.getGridHeight();
      this.gridWidth = this.getGridWidth();
    },
    _computeColIndexes: function(i = -1, newX = -1, newY = -1) {
      var colIndexes = {};

      var self = this;
      this.layout.forEach(function(item) {
          var colIndex = item.x;
          var rowIndex = item.y;
          if (item.i == i) {
            colIndex = newX;
            rowIndex = newY;
          }
          const colIndexStr = colIndex.toString();
          if (!(colIndexStr in colIndexes)) {
            colIndexes[colIndexStr] = []
            colIndexes[colIndex].push(item.i);
          } else {
            var idx = 0;
            colIndexes[colIndexStr].forEach(function(itemIdx){
              if (self.layout[itemIdx].y >= rowIndex) {
                if (colIndexes[colIndex].indexOf(item.i) < 0)
                  colIndexes[colIndex].splice(idx, 0, item.i);
                return;
              }
              idx++;
            });
            if (colIndexes[colIndex].indexOf(item.i) < 0)
              colIndexes[colIndex].push(item.i);
          }
      });
      const log = JSON.stringify(colIndexes);
      console.log(`colIndexes = ${log}`);
      return colIndexes;
    },
    computeMovedLayout: function(i, newX, newY) {
      var colIndexes = this._computeColIndexes(i, newX, newY);

      this.refresh(colIndexes);
    },
    computeMovingLayout: function() {
      var colIndexes = this._computeColIndexes();
      Object.keys(colIndexes).forEach(function(k) {
        colIndexes[k].push("-1");
      });

      this.refresh(colIndexes);
    },
    refresh: function(colIndexes) {
      this.colNum = Object.keys(colIndexes).length;
      var self = this;
      var colIdx = 0;
      Object.keys(colIndexes).forEach(function(k) {
        const col = colIndexes[k];
        const rowCount = col.length;
        if (rowCount != 0) {
          var newHeight = 1 / rowCount;
          var newWidth = 1;

          var rowIdx = 0;
          col.forEach(function(index){
            if (self.layout[index] != undefined) {
              self.layout[index].h = newHeight;
              self.layout[index].w = newWidth;
              self.layout[index].x = colIdx;
              self.layout[index].y = rowIdx;
            }
            rowIdx++;
          });
          colIdx++;
        }
      });
      const log = JSON.stringify(this.layout);
      console.log(`layout = ${log}`);
    },
    movedEvent: function(i, newX, newY){
      if (this.isMoving) {
        this.colNum -= 1;
        this.isMoving = false;
      }
      console.log("MOVED i=" + i + ", X=" + newX + ", Y=" + newY);
      this.computeMovedLayout(i, newX, newY);
    },
    moveEvent: function(/*i, newX, newY*/){
      if (!this.isMoving) {
        this.colNum += 1;
        //this.computeMovingLayout();
        this.isMoving = true;
      }
      //console.log("MOVE i=" + i + ", X=" + newX + ", Y=" + newY);
      //this.computeMovedLayout(i, newX, newY);
    },
    removeItem: function (val) {
      const index = this.layout.map(item => item.i).indexOf(val);
      this.layout.splice(index, 1);
    },
    getGridHeight: function() {
      const mainDiv = document.getElementById("main");
      if (mainDiv) {
        return (mainDiv.offsetHeight - this.titleHeight - 20)/2; //20 for margins
      }
      return 1;
    },
    getGridWidth: function() {
      const mainDiv = document.getElementById("main");
      if (mainDiv) {
        return mainDiv.offsetWidth;
      }
      return 1;
    },
    addHorItem: function(isTiny = false) {
      this.colNum += 1;
      const newX = this.colNum - 1;

      var width = 1
      if (isTiny)
        width = 0.01

      this.layout.push({
        x: newX,
        y: 0,
        w: width,
        h: 1,
        i: this.index,
      });
      this.index++;
      const log = JSON.stringify(this.layout);
      console.log(`colIndexes = ${log}`);
      this.computeMovedLayout(this.index, newX, 0)
    },
    initMainElement: function() {
      this.addHorItem();
    }
  },
  data: () => ({
      layout: [
      ],
      draggable: true,
      resizable: false,
      colNum: 0,
      maxRows: 1,
      titleHeight:40,
      gridHeight: 1,
      gridWidth: 1,
      index: 0,
      isMoving: false,
      nextColIdx: 1,
      nextRowIdx: 1,
  }),
}
</script>

<style>
.layoutJSON {
    background: #ddd;
    border: 1px solid black;
    margin-top: 10px;
    padding: 10px;
}
.columns {
    -moz-columns: 120px;
    -webkit-columns: 120px;
    columns: 120px;
}
/*************************************/
.remove {
    position: absolute;
    right: 2px;
    top: 0;
    cursor: pointer;
}
.vue-grid-layout {
    background: #eee;
}
.vue-grid-item:not(.vue-grid-placeholder) {
    background: #ccc;
    border: 1px solid black;
}
.vue-grid-item .resizing {
    opacity: 0.9;
}
.vue-grid-item .static {
    background: #cce;
}
.vue-grid-item .text {
    font-size: 24px;
    text-align: center;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    height: 100%;
    width: 100%;
}
.vue-grid-item .no-drag {
    height: 100%;
    width: 100%;
}
.vue-grid-item .minMax {
    font-size: 12px;
}
.vue-grid-item .add {
    cursor: pointer;
}
.vue-draggable-handle {
    position: absolute;
    width: 20px;
    height: 20px;
    top: 0;
    left: 0;
    background: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='10'><circle cx='5' cy='5' r='5' fill='#999999'/></svg>") no-repeat;
    background-position: bottom right;
    padding: 0 8px 8px 0;
    background-repeat: no-repeat;
    background-origin: content-box;
    box-sizing: border-box;
    cursor: pointer;
}
</style>
