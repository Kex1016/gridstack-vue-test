<template>
  <div id="app" class="h-screen w-screen flex flex-col items-center bg-gray-100 p-10">
    <div class="flex justify-center">
      <button
        @click="resetGrid"
        class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
      >Reset</button>
      <button
        @click="saveGrid"
        class="ml-2 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
      >Save</button>
      <button
        @click="addWidget"
        class="ml-2 border border-blue-500 hover:border-blue-700 text-blue-500 font-bold py-2 px-4 rounded"
      >Add Widget</button>
    </div>

    <div class="grid-stack h-full w-full">
      <widget v-for="widget in widgets" :widget="widget" :key="widget.text"/>
    </div>
  </div>
</template>

<script>
import cloneDeep from "lodash/cloneDeep";
import Widget from "./components/Widget";
let initialWidgets = [
  {
    id: "widget0",
    text: "first widget",
    layout: {
      x: 0,
      y: 0,
      width: 4,
      height: 2
    }
  },
  {
    id: "widget1",
    text: "second widget",
    layout: {
      x: 4,
      y: 0,
      width: 4,
      height: 4
    }
  }
];
let storageWidgets = localStorage.getItem("widgets");
if (storageWidgets) {
  storageWidgets = JSON.parse(storageWidgets);
} else {
  storageWidgets = cloneDeep(initialWidgets);
}
export default {
  name: "App",
  components: {
    Widget
  },
  data() {
    return {
      grid: null,
      widgets: storageWidgets
    };
  },
  methods: {
    resetGrid() {
      this.widgets = cloneDeep(initialWidgets);
      localStorage.setItem("widgets", JSON.stringify(this.widgets));
    },
    saveGrid() {
      this.grid.engine.nodes.forEach(node => {
        const widget = this.widgets.find(w => w.id === node.id);
        if (widget) {
          widget.layout = {
            x: node.x,
            y: node.y,
            width: node.width,
            height: node.height
          };
        }
      });
      localStorage.setItem("widgets", JSON.stringify(this.widgets));
    },
    addWidget() {
      let index = this.widgets.length;
      // By default grid stack has 12 column layout.
      // We devide by 3 to get the appropriate row/column positioning
      let x = 4 * Math.round(index % 3);
      let y = Math.round(index / 3) * 4;
      const widget = {
        id: `widget${index}`,
        text: `widget ${index}`,
        layout: {
          x,
          y,
          width: 4,
          height: 4
        }
      };
      this.widgets.push(widget);
      this.$nextTick(() => {
        debugger;
        this.grid.makeWidget(`#${widget.id}`);
      });
    }
  },
  mounted() {
    this.grid = window.GridStack.init({
      acceptWidgets: true
    });
    window.grid = this.grid;
  }
};
</script>
