<template>
  <div class="el-color-selector">
    <el-select
      v-model="selected"
      placeholder="Select"
      size="mini"
      class="el-color-selector__select"
      @change="changeColorPack">
      <el-option
        v-for="(select, index) in selectors"
        :key="index"
        :label="select.title"
        :value="index"/>
    </el-select>
    
      <div class="el-color-predefine">
        <!-- <div> -->
          <transition-group name="el-zoom-in-center" tag="div" class="el-color-predefine__colors">
            <div class="el-color-predefine__color-selector"
                :class="{'selected': item.selected, 'is-alpha': item._alpha < 100}"
                v-for="(item, index) in rgbaColors"
                :key="colors[index]"
                @click="handleSelect(index)">
              <div :style="{'background-color': item.value}">
              </div>
            </div>
          </transition-group>
        <!-- </div> -->
      </div>
    
  </div>
</template>

<script>
import Color from '../color';

export default {
  props: {
    selectors: { type: Array, required: true },
    color: { required: true }
  },

  data() {
    return {
      selected: 0,
      colors: [],
      rgbaColors: [],
    }
  },

  created() {
    this.colors = this.selectors[this.selected].colors;
    this.rgbaColors = this.parseColors(this.colors, this.color);
  },

  methods: {
    changeColorPack(val) {
      this.colors = this.selectors[val].colors;
    },

    handleSelect(index) {
      this.color.fromString(this.colors[index]);
    },
    parseColors(colors, color) {
      return colors.map(value => {
        const c = new Color();
        c.enableAlpha = true;
        c.format = 'rgba';
        c.fromString(value);
        c.selected = c.value === color.value;
        return c;
      });
    }
  },
  watch: {
    '$parent.currentColor'(val) {
      const color = new Color();
      color.fromString(val);

      this.rgbaColors.forEach(item => {
        item.selected = color.compare(item);
      });
    },
    colors(newVal) {
      this.rgbaColors = this.parseColors(newVal, this.color);
    },
    color(newVal) {
      this.rgbaColors = this.parseColors(this.colors, newVal);
    }
  }
}
</script>

<style>
  .el-color-selector__select {
    padding-top: 10px;
  }

  .el-color-selector {
    border-top: 2px solid black;
    margin: 10px -5px 0 -5px;
  }
</style>
