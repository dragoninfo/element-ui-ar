<template>
  <div class="el-collapse-item"
    :class="{'is-active': isActive, 'is-disabled': disabled }">
    <div
      role="tab"
      :aria-expanded="isActive"
      :aria-controls="`el-collapse-content-${id}`"
      :aria-describedby ="`el-collapse-content-${id}`"
    >
      <div
        class="el-collapse-item__header"
        @click="handleHeaderClick"
        role="button"
        :id="`el-collapse-head-${id}`"
        :tabindex="disabled ? undefined : 0"
        @keyup.space.enter.stop="handleEnterClick"
        :class="{
          'focusing': focusing,
          'is-active': isActive
        }"
        @focus="handleFocus"
        @blur="focusing = false"
      >
        <slot name="title">{{title}}</slot>
        <i
          class="el-collapse-item__arrow"
          :class="{'is-active': isActive, 'el-icon-arrow-right': !isRtl, 'el-icon-arrow-left': isRtl,}">
        </i>
      </div>
    </div>
    <el-collapse-transition>
      <div
        class="el-collapse-item__wrap"
        v-show="isActive"
        role="tabpanel"
        :aria-hidden="!isActive"
        :aria-labelledby="`el-collapse-head-${id}`"
        :id="`el-collapse-content-${id}`"
      >
        <div class="el-collapse-item__content">
          <slot></slot>
        </div>
      </div>
    </el-collapse-transition>
  </div>
</template>
<script>
  import ElCollapseTransition from 'element-ui-international/src/transitions/collapse-transition';
  import Emitter from 'element-ui-international/src/mixins/emitter';
  import { generateId, isRtl } from 'element-ui-international/src/utils/util';

  export default {
    name: 'ElCollapseItem',

    componentName: 'ElCollapseItem',

    mixins: [Emitter],

    components: { ElCollapseTransition },

    data() {
      return {
        contentWrapStyle: {
          height: 'auto',
          display: 'block'
        },
        contentHeight: 0,
        focusing: false,
        isClick: false,
        id: generateId()
      };
    },

    inject: ['collapse'],

    props: {
      title: String,
      name: {
        type: [String, Number],
        default() {
          return this._uid;
        }
      },
      disabled: Boolean
    },

    computed: {
      isActive() {
        return this.collapse.activeNames.indexOf(this.name) > -1;
      },
      // TODO: rtl el-Collapse 折叠面板 wenzc -- start
      isRtl() {
        return isRtl();
      }
      // TODO: rtl el-Collapse 折叠面板 wenzc -- end
    },

    methods: {
      handleFocus() {
        setTimeout(() => {
          if (!this.isClick) {
            this.focusing = true;
          } else {
            this.isClick = false;
          }
        }, 50);
      },
      handleHeaderClick() {
        if (this.disabled) return;
        this.dispatch('ElCollapse', 'item-click', this);
        this.focusing = false;
        this.isClick = true;
      },
      handleEnterClick() {
        this.dispatch('ElCollapse', 'item-click', this);
      }
    }
  };
</script>
