<template>
  <div
    ref="convstooltip"
    class="con-vs-tooltip"
    v-on="listeners">
    <transition name="tooltip-fade">
      <div
        v-show="activeTooltip"
        ref="vstooltip"
        :class="[`vs-tooltip-${positionx || position}`,`vs-tooltip-${color}`, {'after-none': noneAfter}]"
        :style="style"
        class="vs-tooltip">
        <slot name="content">
          <h4 v-if="title">
            {{title}}
          </h4>
          {{text}}
        </slot>
      </div>
    </transition>
    <slot></slot>
  </div>
</template>
<script>
  import utils from '../../utils';
  import _color from '../../utils/color.js';

  export default {
    name: 'VsTooltip',
    props: {
      title: {
        default: null,
        type: [String, Number],
      },
      text: {
        default: null,
        type: [String, Number],
      },
      color: {
        default: null,
        type: String,
      },
      position: {
        default: 'top',
        type: String,
      },
      delay: {
        default: '0s',
        type: [Number, String],
      },
      active: {
        default: true,
        type: Boolean,
      },
      trigger: {
        type: String,
        default: 'hover',
      },
      value: Boolean,
    },
    data() {
      return {
        cords: {},
        activeTooltip: this.value,
        widthx: 'auto',
        positionx: null,
        noneAfter: false,
      };
    },
    computed: {
      listeners() {
        return this.trigger === 'hover'
          ? {
            mouseenter: this.mouseEnter,
            mouseleave: this.mouseLeave,
          }
          : {
            click: this.toggle,
          };
      },
      style() {
        const background = _color.getColor(this.color, 1);
        return {
          left: this.cords.left,
          top: this.cords.top,
          transitionDelay: this.activeTooltip ? this.delay : '0s',
          background,
          color: _color.contrastColor(background),
          width: this.widthx,
        };
      },
    },
    watch: {
      value(val) {
        if (this.activeTooltip !== val) {
          this.toggle();
        }
      },
      activeTooltip(val) {
        if (this.value !== val) {
          this.$emit('input', val);
        }
      },
    },
    updated() {
      if (this.activeTooltip && !this.$slots.default) {
        this.toggle();
      }
    },
    beforeDestroy() {
      if (this.activeTooltip) {
        this.toggle();
      }
    },
    methods: {
      mouseEnter() {
        if (!this.activeTooltip) {
          this.toggle();
        }
      },
      mouseLeave() {
        if (this.activeTooltip) {
          this.toggle();
        }
      },
      toggle() {
        if (!this.activeTooltip) {
          if (this.active) {
            this.activeTooltip = true;
            this.$emit('open');
            this.$nextTick(() => {
              utils.insertBody(this.$refs.vstooltip);
              this.changePosition(this.$refs.convstooltip, this.$refs.vstooltip);
            });
          }
        } else {
          this.activeTooltip = false;
          this.$emit('close');
          if (this.$refs.vstooltip) {
            utils.removeBody(this.$refs.vstooltip);
          }
        }
      },
      changePosition(elxEvent, tooltip) {
        this.noneAfter = false;
        this.positionx = null;
        const elx = elxEvent.closest('.con-vs-tooltip');
        const scrollTopx = window.pageYOffset || document.documentElement.scrollTop;
        let topx = elx.getBoundingClientRect().top + scrollTopx - tooltip.clientHeight - 4;
        let leftx = elx.getBoundingClientRect().left - tooltip.clientWidth / 2 + elx.clientWidth / 2;
        const widthx = elx.clientWidth;

        if (this.position === 'bottom') {
          topx = elx.getBoundingClientRect().top + scrollTopx + elx.clientHeight + 4;
        } else if (this.position === 'left') {
          leftx = elx.getBoundingClientRect().left - tooltip.clientWidth - 4;
          topx = elx.getBoundingClientRect().top + scrollTopx + (elx.clientHeight / 2) - (tooltip.clientHeight / 2);
          if (leftx < 0) {
            leftx = elx.getBoundingClientRect().left;
            topx = elx.getBoundingClientRect().top + scrollTopx + elx.clientHeight + 4;
            this.positionx = 'bottom';
            this.noneAfter = true;
          }
        } else if (this.position === 'right') {
          leftx = elx.getBoundingClientRect().left + elx.clientWidth + 4;
          topx = elx.getBoundingClientRect().top + scrollTopx + (elx.clientHeight / 2) - (tooltip.clientHeight / 2);
          if (window.innerWidth - (leftx + tooltip.clientWidth) <= 20) {
            leftx = elx.getBoundingClientRect().left - tooltip.clientWidth / 2 - 10;
            topx = elx.getBoundingClientRect().top + scrollTopx + elx.clientHeight + 4;
            this.positionx = 'bottom';
            this.noneAfter = true;
          }
        }

        this.cords = {
          left: `${leftx}px`,
          top: `${topx}px`,
          width: `${widthx}px`,
        };
      },
    },
  };
</script>
