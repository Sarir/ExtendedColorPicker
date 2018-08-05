<template>
  <transition name="el-zoom-in-top" @after-leave="doDestroy">
    <div
      class="el-color-dropdown"
      v-show="showPopper">
      <div class="el-color-dropdown__main-wrapper">
        <hue-slider ref="hue" :color="color" vertical style="float: right;"></hue-slider>
        <sv-panel ref="sl" :color="color"></sv-panel>
      </div>
      <alpha-slider v-if="showAlpha" ref="alpha" :color="color"></alpha-slider>
      <predefine v-if="predefine" :color="color" :colors="predefine"></predefine>
      <div class="el-color-dropdown__btns">
        <span v-if="!noButtons" class="el-color-dropdown__value">
          <el-input
            v-model="customInput"
            @keyup.native.enter="handleConfirm"
            @blur="handleConfirm"
            size="mini">
          </el-input>
        </span>
        <span
          v-if="noButtons"
          class="el-color-dropdown__value">
          {{ currentColor }}
        </span>
        <el-button
          v-if="!noButtons"
          size="mini"
          type="text"
          class="el-color-dropdown__link-btn"
          @click="$emit('clear')">
          Clear
        </el-button>
        <el-button
          v-if="!noButtons"
          plain
          size="mini"
          class="el-color-dropdown__btn"
          @click="confirmValue">
          OK
        </el-button>
        <button
          v-if="noButtons"
          class="el-color-dropdown__btn"
          @click="confirmValue">
          +
        </button>
      </div>
      <selectors
        v-if="selectors"
        :color="color"
        :selectors="selectors"
        @change-select="$emit('change-select', $event)"/>
    </div>
  </transition>
</template>

<script>
  import SvPanel from './sv-panel';
  import HueSlider from './hue-slider';
  import AlphaSlider from './alpha-slider';
  import Predefine from './predefine';
  import Selectors from './selectors';
  import Popper from 'element-ui/src/utils/vue-popper';
  import Locale from 'element-ui/src/mixins/locale';
  import ElInput from 'element-ui/packages/input';
  import ElButton from 'element-ui/packages/button';

  export default {
    name: 'el-color-picker-dropdown',
    mixins: [Popper, Locale],

    components: {
      SvPanel,
      HueSlider,
      AlphaSlider,
      ElInput,
      ElButton,
      Predefine,
      Selectors
    },

    props: {
      color: {
        required: true
      },
      showAlpha: Boolean,
      noButtons: Boolean,
      predefine: Array,
      selectors: Array
    },

    data() {
      return {
        customInput: '',
        selected: '',
      };
    },

    computed: {
      currentColor() {
        return this.color.value;
      }
    },

    methods: {
      confirmValue() {
        this.$emit('pick');
      },

      handleConfirm() {
        this.color.fromString(this.customInput);
      }
    },

    mounted() {
      this.$parent.popperElm = this.popperElm = this.$el;
      this.referenceElm = this.$parent.$el;
    },

    watch: {
      showPopper(val) {
        if (val === true) {
          this.$nextTick(() => {
            const { sl, hue, alpha } = this.$refs;
            sl && sl.update();
            hue && hue.update();
            alpha && alpha.update();
          });
        }
      },

      currentColor(val) {
        this.customInput = val;
      }
    }
  };
</script>
