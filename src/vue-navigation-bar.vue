<template>
  <nav v-if="finalOptions != undefined"
       :class="finalOptions.isMobile == 1 ? 'vnb-mobile' : 'vnb'"
       :id="finalOptions.elementId"
       :aria-label="finalOptions.ariaLabelMainNav">

    <brand-image v-if="finalOptions.isMobile == 1 && finalOptions.brandImage" :options="finalOptions" @vnb-item-clicked="vnbItemClicked" />

    <menu-options
      :options="finalOptions"
      :type="'left'"
      @vnb-item-clicked="vnbItemClicked"
    />

    <slot
      v-if="$vssWidth > options.mobileBreakpoint"
      name="custom-section"
    ></slot>

    <menu-options
      :options="finalOptions"
      :type="'right'"
      @vnb-item-clicked="vnbItemClicked"
    />

    <collapse-button
      v-if="
        finalOptions.menuOptionsLeft.length ||
          finalOptions.menuOptionsRight.length
      "
      :options="finalOptions"
      :menuIsVisible="menuIsVisible"
      @collapse-button-clicked="showMobilePopup"
    />

    <popup
      v-if="
        finalOptions.menuOptionsLeft.length ||
          finalOptions.menuOptionsRight.length
      "
      :options="finalOptions"
      :menuIsVisible="menuIsVisible"
      @close-button-clicked="closeMobilePopup"
      @vnb-item-clicked="vnbItemClicked"
    >
      <template v-slot:custom-section>
        <slot name="custom-section"></slot>
      </template>
    </popup>
  </nav>
</template>

<script>
import VueScreenSize from "vue-screen-size";
import uuidV4 from "./common/uuidv4";
import BrandImage from "./components/BrandImage.vue";
import MenuOptions from "./components/MenuOptions.vue";
import CollapseButton from "./components/CollapseButton.vue";
import Popup from "./components/Popup.vue";

export default {
  name: "vue-navigation-bar",
  mixins: [VueScreenSize.VueScreenSizeMixin],
  props: {
    options: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      menuIsVisible: false
    };
  },
  computed: {
    finalOptions() {
      // What we're doing here is giving each top-level menu-option a unique id
      if (this.options !== undefined) {
        if (this.options.menuOptionsLeft) {
          for (let x = 0; x < this.options.menuOptionsLeft.length; x++) {
            this.$set(this.options.menuOptionsLeft[x], "id", uuidV4());
          }
        }
        if (this.options.menuOptionsRight) {
          for (let x = 0; x < this.options.menuOptionsRight.length; x++) {
            this.$set(this.options.menuOptionsRight[x], "id", uuidV4());
          }
        }

        return {
          isMobile: this.options.isMobile,
          theme: this.options.theme ? this.options.theme : 'light',
          elementId: this.options.elementId ? this.options.elementId : uuidV4(),
          isUsingVueRouter: this.options.isUsingVueRouter ? true : false,
          mobileBreakpoint: this.options.mobileBreakpoint
            ? this.options.mobileBreakpoint
            : 992,
          brandImagePath: this.options.brandImagePath
            ? this.options.brandImagePath
            : "/",
          brandImage: this.options.brandImage ? this.options.brandImage : null,
          brandImageAltText: this.options.brandImageAltText
            ? this.options.brandImageAltText
            : "brand-image",
          collapseButtonImageOpen: this.options.collapseButtonImageOpen
            ? this.options.collapseButtonImageOpen
            : null,
          collapseButtonImageClose: this.options.collapseButtonImageClose
            ? this.options.collapseButtonImageClose
            : null,
          collapseButtonOpenColor: this.options.collapseButtonOpenColor
            ? this.options.collapseButtonOpenColor
            : '#373737',
          collapseButtonCloseColor: this.options.collapseButtonCloseColor
            ? this.options.collapseButtonCloseColor
            : '#373737',
          showBrandImageInMobilePopup: this.options.showBrandImageInMobilePopup
            ? true
            : false,
          ariaLabelMainNav: this.options.ariaLabelMainNav
            ? this.options.ariaLabelMainNav
            : "Main Navigation",
          tooltipAnimationType: this.options.tooltipAnimationType
            ? this.options.tooltipAnimationType
            : "shift-away",
          tooltipPlacement: this.options.tooltipPlacement || "bottom",
          menuOptionsLeft: this.options.menuOptionsLeft
            ? this.options.menuOptionsLeft
            : [],
          menuOptionsRight: this.options.menuOptionsRight
            ? this.options.menuOptionsRight
            : []
        };
      }
    }
  },
  methods: {
    closeMobilePopup() {
      this.menuIsVisible = false;
      this.$emit("vnb-mobile-popup-hidden");
    },
    showMobilePopup() {
      this.menuIsVisible = true;
      this.$emit("vnb-mobile-popup-shown");
    },
    vnbItemClicked(text, path) {
      this.$emit("vnb-item-clicked", text, path);
    }
  },
  components: {
    BrandImage,
    MenuOptions,
    CollapseButton,
    Popup
  }
};
</script>

<style lang="scss">
@import "./assets/css/main.scss";

.vnb {
  * {
    box-sizing: border-box;
  }

  background: $white;
  padding-top: 15px;
  padding-bottom: 15px;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;

  a {
    text-decoration: none;
  }
}

.tippy-tooltip {
  padding: 0;
}

.vnb-image {
  max-width: 100%;
  height: auto;
}
</style>
