<template>

  <component
    :is="htmlTag"
    ref="button"
    dir="auto"
    :class="buttonClasses"
    :type="type"
    :disabled="disabled"
    tabindex="0"
    @click="handleClick"
    @keyup.enter.stop.prevent="handlePressEnter"
  >
    <slot name="icon">
      <KIcon v-if="icon" :icon="icon" :color="$themeTokens.textInverted" />
    </slot>

    <slot v-if="$slots.default"></slot>

    <template v-else>
      <span>{{ text }}</span>
    </template>

    <slot name="iconAfter">
      <KIcon v-if="iconAfter" :icon="iconAfter" :color="$themeTokens.textInverted" />
    </slot>

    <!-- Dropdown arrow icon -->
    <KIcon
      v-if="hasDropdown"
      icon="dropdown"
      class="dropdown-arrow"
      :style="arrowStyles"
      style="width: 24px; height: 24px;"
    />
  </component>

</template>


<script>

  import { validator } from './appearances.js';
  import buttonMixin from './buttonMixin.js';

  /**
   * The KButton component is used to trigger actions
   */
  export default {
    name: 'KButton',
    mixins: [buttonMixin],
    props: {
      /**
       * Button appearance: 'raised-button', 'flat-button', or 'basic-link'
       */
      appearance: {
        type: String,
        default: 'raised-button', // changes default of 'appearance' prop provided by mixin
        validator,
      },
      /**
       * Whether or not button is disabled
       */
      disabled: {
        type: Boolean,
        default: false,
      },
      /**
       * HTML button 'type' attribute (e.g. 'submit', 'reset')
       */
      type: {
        type: String,
        default: 'button',
      },
      /**
       * @private
       * Adds a dropdown arrow
       */
      hasDropdown: {
        type: Boolean,
        required: false,
        default: false,
      },
      /**
       * If provided, prepends a KIcon to the text in the button
       */
      icon: {
        type: String,
        required: false,
      },
      /**
       * If provided, appends an KIcon to the text in the button.
       */
      iconAfter: {
        type: String,
        required: false,
      },
    },
    computed: {
      htmlTag() {
        // Necessary to allow basic links to be rendered as 'inline' instead of
        // 'inline-block': https://stackoverflow.com/a/27770128
        if (this.appearance === 'basic-link') {
          return 'a';
        }
        return 'button';
      },
      arrowStyles() {
        if (this.primary) {
          return {
            fill: this.$themeTokens.textInverted,
          };
        }
        return {};
      },
    },
    methods: {
      handleClick(event) {
        /**
         * Emitted when the button is triggered
         */
        this.blurWhenClicked();
        this.$emit('click', event);
      },
      handlePressEnter(event) {
        this.blurWhenClicked();
        // HACK: for 'a' tags, the 'click' event is not getting fired
        if (this.htmlTag === 'a') {
          this.$emit('click', event);
        }
      },
      // To prevent the <a> from maintaining focus when link does not
      // destroy parent component (e.g. opens a modal), we need to blur it,
      // because it will be "clicked" again when the user hits the Enter key.
      blurWhenClicked() {
        if (this.htmlTag === 'a') {
          this.$el.blur();
        }
      },
    },
  };

</script>


<style lang="scss" scoped>

  @import './buttons';

  .dropdown-arrow {
    position: relative;
    top: 6px;
  }

</style>
