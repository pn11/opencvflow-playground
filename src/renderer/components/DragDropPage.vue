<template>
  <div>Vue-konva のサンプルをコピペ。v-layer を使うために webpack.renderer.config.js を修正する必要があることに注意。(<a href="https://github.com/konvajs/vue-konva/issues/14">https://github.com/konvajs/vue-konva/issues/14</a>)
  <br>
  <router-link to="/">ホームへ</router-link>
    <v-stage
      ref="stage"
      :config="configKonva"
      @dragstart="handleDragstart"
      @dragend="handleDragend"
    >
      <v-layer ref="layer">
        <v-star v-for="item in list" :key="item.id" :config="item"></v-star>
      </v-layer>
      <v-layer ref="dragLayer"></v-layer>
    </v-stage>
  </div>
</template>

<script>

import Vue from 'vue';
//import App from 'DragDropPage';

import VueKonva from 'vue-konva';

Vue.use(VueKonva);

const width = window.innerWidth;
const height = window.innerHeight;
let vm = {};

export default {
  name: "DragDropPage",
  display: "Simple",
  order: 0,
  data() {
    return {
      list: [],
      configKonva: {
        width: width,
        height: height
      }
    };
  },
  methods: {
    handleDragstart(e) {
      const shape = e.target;
      const dragLayer = vm.$refs.dragLayer.getNode();
      const stage = vm.$refs.stage.getNode();
      // moving to another layer will improve dragging performance
      shape.moveTo(dragLayer);
      stage.draw();
      shape.setAttrs({
        shadowOffsetX: 15,
        shadowOffsetY: 15,
        scaleX: shape.getAttr("startScale") * 1.2,
        scaleY: shape.getAttr("startScale") * 1.2
      });
    },
    handleDragend(e) {
      const shape = e.target;
      const layer = vm.$refs.layer.getNode();
      const stage = vm.$refs.stage.getNode();
      shape.moveTo(layer);
      stage.draw();
      shape.to({
        duration: 0.5,
        easing: Konva.Easings.ElasticEaseOut,
        scaleX: shape.getAttr("startScale"),
        scaleY: shape.getAttr("startScale"),
        shadowOffsetX: 5,
        shadowOffsetY: 5
      });
    }
  },
  mounted() {
    vm = this;
    for (let n = 0; n < 30; n++) {
      const scale = Math.random();
      const stage = vm.$refs.stage.getStage();
      vm.list.push({
        x: Math.random() * stage.width(),
        y: Math.random() * stage.height(),
        rotation: Math.random() * 180,
        numPoints: 5,
        innerRadius: 30,
        outerRadius: 50,
        fill: "#89b717",
        opacity: 0.8,
        draggable: true,
        scaleX: scale,
        scaleY: scale,
        shadowColor: "black",
        shadowBlur: 10,
        shadowOffsetX: 5,
        shadowOffsetY: 5,
        shadowOpacity: 0.6,
        startScale: scale
      });
    }
  }
};
</script>