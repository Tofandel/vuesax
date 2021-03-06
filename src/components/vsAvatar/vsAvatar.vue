<template lang="html">
  <div
    v-bind="$attrs"
    :class="avatarClass"
    :style="avatarStyle"
    class="con-vs-avatar"
    v-on="$listeners">
    <div
      v-if="badge && badge > 0"
      :class="badgeClass"
      :style="badgeStyle"
      class="dot-count vs-avatar--count">
      {{typeof badge !== 'boolean' ? badge : null}}
    </div>
    <div
      v-if="src"
      class="con-img vs-avatar--con-img">
      <img
        :alt="text"
        :src="src">
    </div>
    <span
      v-else
      :class="[text ? '' : iconPack, text || iconPack === 'material-icons' ? '' : icon, textClass]"
      :style="textStyle"
      :title="text"
      class="vs-avatar--text notranslate"
      translate="no"> {{text ? returnText : (iconPack === 'material-icons' ? icon : '')}} </span>
  </div>
</template>

<script>
  import _color from '../../utils/color.js';

  export default {
    name: 'VsAvatar',
    props: {
      badge: {
        type: [Boolean, String, Number],
        default: false,
      },
      badgeColor: {
        default: 'danger',
        type: String,
      },
      size: {
        type: String,
        default: null,
      },
      src: {
        type: String,
        default: null,
      },
      icon: {
        type: String,
        default: 'person',
      },
      iconPack: {
        type: String,
        default: 'material-icons',
      },
      textColor: {
        type: String,
        default: 'rgb(255, 255, 255)',
      },
      text: {
        type: [String, Number],
        default: null,
      },
      color: {
        type: String,
        default: 'rgb(195, 195, 195)',
      },
    },
    computed: {
      avatarClass() {
        const classes = {};
        if (this.size) {
          classes[this.size] = true;
        }
        if (_color.isColor(this.color)) {
          classes[`con-vs-avatar-${this.color}`] = true;
        }
        return classes;
      },
      avatarStyle() {
        const pxSize = /[px]/.test(this.size);
        const style = {
          width: pxSize ? this.size : null,
          height: pxSize ? this.size : null,
          fontSize: pxSize ? parseInt(this.size) / 3 + 'px' : null,
        };
        if (!_color.isColor(this.color)) {
          style.background = _color.getColor(this.color);
        }
        return style;
      },
      badgeClass() {
        const classes = {
          badgeNumber: (typeof badge !== 'boolean'),
        };
        if (_color.isColor(this.badgeColor)) {
          classes[`dot-count-${this.badgeColor}`] = true;
        }
        return classes;
      },
      badgeStyle() {
        const style = {};
        if (!_color.isColor(this.badgeColor)) {
          style.background = _color.getColor(this.badgeColor);
        }
        return style;
      },
      textClass() {
        const classes = {};
        if (_color.isColor(this.textColor)) {
          classes[`vs-avatar-text-${this.textColor}`] = true;
        }
        return classes;
      },
      textStyle() {
        const style = {
          transform: `translate(-50%,-50%) scale(${this.returnScale})`,
        };
        if (!_color.isColor(this.textColor)) {
          style.color = _color.getColor(this.textColor);
        }
        return style;
      },
      returnText() {
        if (this.text.length <= 5) {
          return this.text;
        }
        const exp = /\s/g;
        let letras = '';
        if (exp.test(this.text)) {
          this.text.split(exp).forEach((word) => {
            letras += word[0].toUpperCase();
          });
        } else {
          letras = this.text[0].toUpperCase();
        }
        return letras.length > 5 ? letras[0] : letras;
      },
      returnScale() {
        if (!this.text) return 1;
        const lengthx = this.returnText.length;
        if (lengthx <= 5 && lengthx > 1) {
          return lengthx / (lengthx * 1.50);
        } else {
          return 1;
        }
      },
    },
  };
</script>
