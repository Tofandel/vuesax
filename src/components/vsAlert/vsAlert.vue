<template lang="html">
  <transition
    @enter="enter"
    @leave="leave"
    @before-enter="beforeEnter">
    <div
      v-if="active"
      ref="alert"
      v-bind="$attrs"
      :class="[`con-vs-alert-${color}`,{
        'con-icon':icon,
      }]"
      :style="styleAlert"
      class="con-vs-alert"
      v-on="$listeners">
      <div
        v-if="closable"
        class="con-x vs-alert--close"
        @click="$emit('update:active',false)">
        <vs-icon
          :icon="closeIcon"
          :icon-pack="iconPack"/>
      </div>

      <h4
        v-if="title"
        :style="styleTitle"
        class="titlex vs-alert--title"
        v-text="title"></h4>

      <div
        :class="{'con-icon': icon}"
        class="vs-alert">
        <vs-icon
          v-if="icon"
          :icon="icon"
          :icon-pack="iconPack"
          class="icon-alert"/>
        <slot></slot>
      </div>
    </div>
  </transition>
</template>

<script>
  import _color from '../../utils/color.js';

  export default {
    name: 'VsAlert',
    props: {
      active: {
        type: Boolean,
        default: true,
      },
      title: {
        type: String,
        default: null,
      },
      closable: {
        type: Boolean,
        default: false,
      },
      color: {
        type: String,
        default: 'primary',
      },
      margin: {
        type: [String, Boolean],
        default: '10px',
      },
      icon: {
        type: String,
        default: null,
      },
      closeIcon: {
        type: String,
        default: 'close',
      },
      iconPack: {
        type: String,
        default: 'material-icons',
      },
    },

    computed: {
      styleAlert() {
        return {
          background: _color.getColor(this.color, 0.15),
          boxShadow: `0px 0px 25px 0px ${_color.getColor(this.color, 0.15)}`,
          color: _color.getColor(this.color, 1),
        };
      },
      styleTitle() {
        return {
          boxShadow: `0px 6px 15px -7px ${_color.getColor(this.color, 0.4)}`,
        };
      },
    },
    methods: {
      beforeEnter(el) {
        el.style.height = 0;
        el.style.opacity = 0;
      },
      enter(el, done) {
        const h = this.$refs.alert.scrollHeight;
        this.$refs.alert.style.height = h + 'px';
        el.style.opacity = 1;
        done();
      },
      leave(el) {
        this.$refs.alert.style.height = 0 + 'px';
        el.style.opacity = 0;
      },
    },
  };
</script>
