<template>
  <div
    :class="{'vs-sidebar-group-open' : openItems}"
    class="vs-sidebar-group"
    @mouseout="mouseout"
    @mouseover="mouseover">
    <h4 @click="clickGroup">
      {{title}}
      <vs-icon>keyboard_arrow_down</vs-icon>
    </h4>
    <span class="vs-sidebar--tooltip">{{title}}</span>
    <ul
      ref="items"
      :style="styleItems"
      class="vs-sidebar--group-items">
      <slot></slot>
    </ul>
  </div>
</template>
<script>
  export default {
    name: 'VsSidebarGroup',
    props: {
      collapsed: Boolean,
      title: {
        default: null,
        type: String,
      },
      openHover: Boolean,
      open: Boolean,
    },
    data: () => ({
      maxHeight: '0px',
      openItems: false,
    }),
    computed: {
      styleItems() {
        return {
          maxHeight: this.maxHeight,
        };
      },
    },
    watch: {
      maxHeight() {
        this.openItems = this.maxHeight !== '0px';
      },
    },
    mounted() {
      this.openItems = this.open;
      if (this.open) {
        this.maxHeight = 'none';
      }
    },
    methods: {
      getActive() {
        return this.$parent.getActive();
      },
      setIndexActive(index) {
        this.$parent.setIndexActive(index);
      },
      clickGroup() {
        if (!this.openHover) {
          const scrollHeight = this.$refs.items.scrollHeight;
          if (this.maxHeight === '0px') {
            this.maxHeight = `${scrollHeight}px`;
            setTimeout(() => {
              this.maxHeight = 'none';
            }, 300);
          } else {
            this.maxHeight = `${scrollHeight}px`;
            setTimeout(() => {
              this.maxHeight = `${0}px`;
            }, 50);
          }
        }
      },
      mouseover() {
        if (this.openHover) {
          const scrollHeight = this.$refs.items.scrollHeight;
          this.maxHeight = `${scrollHeight}px`;
        }
      },
      mouseout() {
        if (this.openHover) {
          const scrollHeight = 0;
          this.maxHeight = `${scrollHeight}px`;
        }
      },
    },
  };
</script>
