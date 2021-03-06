<template>
  <vs-row
    :vs-justify="description ? 'space-between' : 'center'"
    vs-type="flex"
    vs-w="12">
    <vs-col
      v-if="description"
      class="vs-pagination--mb"
      vs-align="center"
      vs-justify="flex-start"
      vs-lg="6"
      vs-sm="12"
      vs-type="flex"
      vs-xs="12">
      <div>
        <span
          style="margin-right:5px"> {{descriptionTitle}}: {{minRows}} - {{maxRows}} {{descriptionConnector}} {{sizeArray}} |
          {{descriptionBody}}:

        </span>
        <ul class="vs-pagination--array">
          <li
            v-for="(row,index) in descriptionItems"
            :key="index">
            <span
              :class="[`vs-description-${color}`,{ 'vs-pagination--bold': (index===indexRows)}]"
              :style="styleDescription"
              @click="changeRowMaxItems(index)">{{row}}</span><span v-if="index !== (descriptionItems.length - 1)"> , </span>
          </li>
        </ul>
      </div>
    </vs-col>
    <vs-col
      :vs-lg="description ? 6 : 12"
      class="vs-pagination--mb"
      vs-align="center"
      vs-justify="flex-end"
      vs-sm="12"
      vs-type="flex"
      vs-xs="12">
      <div
        :class="[`vs-pagination-${color}`]"
        :style="stylePagination"
        class="con-vs-pagination">
        <nav class="vs-pagination--nav">
          <button
            :class="{disabled:current <= 1 ? 'disabled' : null}"
            :disabled="current === 1"
            class="vs-pagination--buttons btn-prev-pagination vs-pagination--button-prev"
            @click="prevPage">
            <vs-icon
              :icon="prevIcon ? prevIcon : defaultPrevIcon"
              :icon-pack="iconPack"/>
          </button>
          <ul class="vs-pagination--ul">
            <li
              v-for="(page, index) in pages"
              :key="index"
              :class="{'is-current': page === current}"
              class="item-pagination vs-pagination--li"
              @click="goTo(page)">
              <span> {{page}} </span>

              <div class="effect"></div>
            </li>
          </ul>
          <!-- :style="styleBtn" -->
          <button
            :class="{disabled:current === total ? 'disabled' : null}"
            :disabled="current === total"
            class="vs-pagination--buttons btn-next-pagination vs-pagination--button-next"
            @click="nextPage">
            <vs-icon
              :icon="nextIcon ? nextIcon : defaultNextIcon"
              :icon-pack="iconPack"/>
          </button>
          <input
            v-if="goto"
            v-model="go"
            :max="total"
            class="vs-pagination--input-goto"
            min="1"
            type="number"
            @change="goTo">
        </nav>
      </div>
    </vs-col>
  </vs-row>
</template>
<script>
  import _color from '../../utils/color.js';

  export default {
    name: 'VsPagination',
    props: {
      color: {
        type: String,
        default: 'primary',
      },
      total: {
        type: Number,
        required: true,
      },
      sizeArray: {
        type: Number,
        required: false,
      },
      maxItems: {
        type: [Number, String],
        required: false,
      },
      value: {
        type: Number,
        required: true,
        default: 1,
      },
      description: Boolean,
      max: {
        type: Number,
        default() {
          return this.description ? 9 : 13;
        },
      },
      goto: {
        type: Boolean,
      },
      type: {
        type: String,
      },
      prevIcon: {
        type: String,
      },
      nextIcon: {
        type: String,
      },
      iconPack: {
        type: String,
        default: 'material-icons',
      },
      descriptionItems: {
        default: () => [],
        type: Array,
      },
      descriptionTitle: {
        type: String,
        default: 'Registries',
      },
      descriptionConnector: {
        type: String,
        default: 'of',
      },
      descriptionBody: {
        type: String,
        default: 'Pages',
      },
    },
    data() {
      return {
        current: this.value,
        go: this.value,
        indexRows: this.descriptionItems.indexOf(this.maxItems),
      };
    },
    computed: {
      maxRows() {
        return ((this.current * this.maxItems) <= this.sizeArray) ? this.current * this.maxItems : this.sizeArray;
      },
      minRows() {
        return ((this.current * this.maxItems) <= this.sizeArray)
          ? (this.maxRows - this.maxItems) + 1
          : ((this.current - 1) * this.maxItems) + 1;
      },
      defaultNextIcon() {
        if (this.$vs.rtl) return 'chevron_left';
        return 'chevron_right';
      },
      defaultPrevIcon() {
        if (this.$vs.rtl) return 'chevron_right';
        return 'chevron_left';
      },
      stylePagination() {
        let style = {};
        if (!_color.isColor(this.color)) {
          style = {
            '--vs-color-pagination': _color.getColor(this.color),
            '--vs-color-pagination-alpha': _color.getColor(this.color, 0.5),
          };
        }
        return style;
      },
      styleDescription() {
        return {
          cursor: 'pointer',
        };
      },
      pages() {
        if (this.total <= Math.max(5, this.max)) {
          return this.setPages(1, this.total);
        }

        if (this.current >= 4 && this.current <= this.total - 3) {
          const incr = (this.max - 4) / 2;
          const pages = [1];

          const min = this.current - Math.ceil(incr);
          const max = this.current + Math.floor(incr);

          if (min > 2) {
            pages.push('...');
          } else if (min === 2) {
            pages.push(2);
          }

          pages.push.apply(pages, this.setPages(
            Math.max(3, min),
            Math.min(this.total - 2, max)),
          );

          if (max < this.total - 1) {
            pages.push('...');
          } else if (max === this.total - 1) {
            pages.push(this.total - 1);
          }
          pages.push(this.total);

          return pages;
        } else if (this.current < 4) {
          return this.max - 2 < this.total - 1
            ? [
              ...this.setPages(1, this.max - 2),
              '...',
              this.total,
            ]
            : [...this.setPages(1, this.max)];
        } else {
          return this.total - this.max > 2
            ? [
              1,
              '...',
              ...this.setPages(this.total - this.max - 2, this.total),
            ]
            : [...this.setPages(1, this.max)];
        }
      },
    },
    watch: {
      current(val) {
        if (val !== this.value) {
          this.$emit('input', val);
          this.$emit('change', val);
        }
      },
      value(val) {
        const pageNum = val < 1 ? 1 : (val <= this.total ? val : this.total);
        this.goTo(pageNum);
      },
    },
    methods: {
      changeRowMaxItems(index) {
        this.indexRows = index;
        this.$emit('changeMaxItems', index);
        this.current = 1;
      },
      isEllipsis(page) {
        return page === '...';
      },
      goTo(page) {
        if (this.isEllipsis(page)) {
          return;
        }
        if (typeof page.target === 'undefined') {
          this.current = page;
        } else {
          const value = parseInt(page.target.value, 10);
          this.go = Math.min(this.total, Math.max(1, value));
          this.current = this.go;
        }
      },
      setPages(start, end) {
        const setPages = [];

        for (; start <= end; start++) {
          setPages.push(start);
        }

        return setPages;
      },
      nextPage() {
        if (this.current < this.total) {
          this.current++;
        }
      },
      prevPage() {
        if (this.current > 1) {
          this.current--;
        }
      },
    },
  };
</script>
