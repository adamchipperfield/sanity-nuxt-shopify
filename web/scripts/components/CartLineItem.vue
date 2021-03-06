<template>
  <div class="cart-line-item" :class="classes">
    <template v-if="isLoading">
      <div class="cart-line-item__thumbnail"></div>

      <div class="cart-line-item__body">
        <div class="cart-line-item__title"></div>
        <div class="cart-line-item__variant"></div>
      </div>
    </template>

    <template v-else>
      <responsive-image
        v-if="lineItem.image"
        :url="lineItem.image.originalSrc"
        :alt="lineItem.image.altText"
        :max-height="150"
        :max-width="125"
      />

      <div class="cart-line-item__body">
        <n-link
          :to="productUrl"
          class="cart-line-item__title"
        >
          {{ lineItem.product.title }}
        </n-link>

        <p class="cart-line-item__variant meta">{{ lineItem.title }}</p>

        <p class="cart-line-item__price meta">
          {{ lineItemPrice }} x {{ lineItem.quantity }}
        </p>

        <button
          class="cart-line-item__remove caption"
          :disabled="remove.disabled"
          @click="handleRemoveEvent"
        >
          {{ remove.label }}
        </button>
      </div>
    </template>
  </div>
</template>

<script>
import { mapActions } from 'vuex'

import ResponsiveImage from '~/components/ResponsiveImage'

export default {
  components: {
    ResponsiveImage
  },

  props: {
    lineItem: {
      type: Object,
      default: () => {}
    },
    isLoading: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      remove: {
        disabled: false,
        label: this.$t('cart.line_item.remove')
      }
    }
  },

  computed: {
    /**
     * Returns the formatted price of the line item.
     * @returns {string}
     */
    lineItemPrice() {
      const price = this.lineItem.presentmentPrices[0].price

      return new Intl.NumberFormat('en-GB', {
        style: 'currency',
        currency: price.currencyCode
      })
        .format(price.amount)
    },

    /**
     * Returns the line item's product URL.
     * @returns {string}
     */
    productUrl() {
      return `/products/${this.lineItem.product.handle}`
    },

    /**
     * Returns the dynamic classes.
     * @returns {object}
     */
    classes() {
      return {
        'is-loading': this.isLoading
      }
    }
  },

  methods: {
    /**
     * Maps the Vuex actions.
     */
    ...mapActions({
      removeLineItem: 'cart/removeLineItem'
    }),

    /**
     * Handles the remove item event.
     */
    handleRemoveEvent() {
      this.remove.disabled = true
      this.remove.label = this.$t('cart.line_item.removing')

      this.removeLineItem(this.lineItem.id).then(() => {
        this.remove.disabled = false
        this.remove.label = this.$t('cart.line_item.remove')
      })
    }
  }
}
</script>

<style lang="scss">
.cart-line-item {
  $parent: &;
  display: flex;

  &__body {
    flex-grow: 1;
    padding-left: $SPACING_L;
  }

  &__title {
    display: block;
    margin: $SPACING_M 0 0;
  }

  &__variant,
  &__price {
    margin: $SPACING_2XS 0;
  }

  &__remove {
    @include button-reset;
    cursor: pointer;
    margin-top: $SPACING_L;
    text-decoration: underline;
  }

  &.is-loading {
    #{$parent}__thumbnail {
      background-color: $COLOR_BACKGROUND_LIGHT;
      border-radius: 5px;
      height: 150px;
      width: 125px;
    }

    #{$parent}__title {
      background-color: $COLOR_BACKGROUND_LIGHT;
      border-radius: 5px;
      height: 20px;
      width: 230px;
    }

    #{$parent}__variant {
      background-color: $COLOR_BACKGROUND_LIGHT;
      border-radius: 5px;
      height: 18px;
      margin-top: $SPACING_S;
      width: 70px;
    }
  }
}
</style>
