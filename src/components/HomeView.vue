<template>
  <v-main id="main" height="100%" width="100%">
  <h1 :style="{height:titleheight+'px', width:'100%'}">Home</h1>
  <grid-layout id="gridLayout"
               :layout.sync="layout"
               :col-num="colNum"
               :row-height="rowHeight"
               :is-draggable="draggable"
               :is-resizable="resizable"
               :vertical-compact="true"
               :use-css-transforms="true"
      :autoSize=false
      :style="{height:'getDefaultRowHeight', width:'100%'}"
   >
     <grid-item v-for="item in layout"
                :key=item.i
                :static="item.static"
                :x="item.x"
                :y="item.y"
                :w="item.w"
                :h="item.h"
                :i="item.i"
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
  mounted: function() {
    this.rowHeight = this.getGridHeight();
  },
  methods:{
    getGridHeight: function() {
      const gridLayout = document.getElementById("main");
      if (gridLayout) {
        return gridLayout.offsetHeight - this.titleHeight - 20; //20 for margins
      }
      return 10;
    },
    getGridWidth: function() {
      const gridLayout = document.getElementById("gridLayout");
      if (gridLayout) {
        return gridLayout.offsetWidth;
      }
      return 10;
    },
    getDefaultRowHeight: function() {
      const main = document.getElementById("main");
      if (main) {
        return main.offsetHeight;
      }
      return 1;
    }
  },
  data: () => ({
      layout: [
          { x: 0, y: 0, w: 1.9, h: 1, i: "0" },
      ],
      draggable: true,
      resizable: true,
      colNum: 2,
      titleHeight:40,
      rowHeight: 10,
      index: 0,
      maxRows: 1,
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
