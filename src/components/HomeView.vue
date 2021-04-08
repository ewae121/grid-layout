<template>
  <v-main id="main" height="100%" width="100%">
  <h1 :style="{height:titleHeight+'px', width:'100%'}">Home</h1>
  <grid-layout id="gridLayout"
               :layout.sync="layout"
               :col-num="colNum"
               :max-rows="2"
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

    for (let i = 0; i < 10; ++i)
      this.addHorItem();
  },
  methods : {
    onResize(/*e*/) {
      this.gridHeight = this.getGridHeight();
      this.gridWidth = this.getGridWidth();
    },
    computeNewRowHeight: function(i, newX) {
      var colIndexes = {};

      this.layout.forEach(function(item) {
          var colIndex = item.x;
          if (item.i == i)
            colIndex = newX
          if (!(colIndex.toString() in colIndexes))
            colIndexes[colIndex.toString()] = []
          colIndexes[colIndex].push(item.i);
      });

      this.colNum = Object.keys(colIndexes).length + 1;
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
            self.layout[index].h = newHeight;
            self.layout[index].w = newWidth;
            self.layout[index].x = colIdx;
            self.layout[index].y = rowIdx;
            rowIdx++;
          });
          colIdx++;
        }
      });
    },
    movedEvent: function(i, newX, newY){
      console.log("MOVED i=" + i + ", X=" + newX + ", Y=" + newY);
      this.computeNewRowHeight(i, newX, newY);
    },
    moveEvent: function(i, newX, newY){
      console.log("MOVE i=" + i + ", X=" + newX + ", Y=" + newY);
      //this.computeNewRowHeight(i, newX, newY);
    },
    getGridHeight: function() {
      const mainDiv = document.getElementById("main");
      if (mainDiv) {
        return mainDiv.offsetHeight - this.titleHeight - 20; //20 for margins
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
    addHorItem: function() {
      // Add a new item. It must have a unique key!
      this.colNum += 1;

      this.index++;
      this.layout.push({
        x: this.nextColIdx,
        y: 0,
        w: 1,
        h: 1,
        i: this.index,
      });
      this.nextColIdx++;
    }
  },
  data: () => ({
      layout: [
          { x: 0, y: 0, w: 1, h: 1, i: 0 },
      ],
      draggable: true,
      resizable: false,
      colNum: 1,
      maxRows: 1,
      titleHeight:40,
      gridHeight: 1,
      gridWidth: 1,
      index: 0,
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
