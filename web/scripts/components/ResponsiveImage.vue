<template>
  <picture
    class="responsive-image"
    :class="classes"
    :style="styles"
  >
    <span
      :style="{
        display: 'block', paddingTop: `${(maxHeight / maxWidth) * 100}%`
      }"
      aria-hidden="true"
    />

    <source
      :data-srcset="getSizedImage(url, '{width}', 'webp')"
      type="image/webp"
    />

    <source
      :data-srcset="getSizedImage(url, '{width}', 'jpg')"
      type="image/jpeg"
    /> 

    <img
      v-if="url"
      class="responsive-image__image lazyload"
      :alt="alt"
      data-sizes="auto"
      :src="getSizedImage(url, 5)"
      :style="imgStyles"
    />
  </picture>
</template>

<script>
import { getSizedImageUrl } from '@shopify/theme-images'

export default {
  props: {
    url: {
      type: String,
      default: ''
    },

    alt: {
      type: String,
      default: ''
    },

    maxHeight: {
      type: Number,
      default: 0
    },

    maxWidth: {
      type: Number,
      default: 0
    },

    fit: {
      type: String,
      default: 'cover'
    }
  },

  computed: {
    /**
     * Returns the classes of the image.
     * @returns {object}
     */
    classes() {
      return {
        'responsive-image--contain': this.fit === 'contain',
        'responsive-image--fit': this.fit === 'fit',
        'responsive-image--fill': this.fit === 'fill'
      }
    },

    /**
     * Returns the styles for the component.
     * @returns {object}
     */
    styles() {
      if (this.maxWidth) {
        return { maxWidth: `${this.maxWidth}px` }
      }
    },

    /**
     * Returns dynamic styles for the image element.
     * @returns {object}
     */
    imgStyles() {
      const styles = {}

      if (this.maxHeight) {
        styles.maxHeight = `${this.maxHeight}px`
      }

      if (this.maxWidth) {
        styles.maxWidth = `${this.maxWidth}px`
      }

      if (!this.maxHeight && !this.maxWidth) {
        styles.position = 'relative'
        styles.display = 'block'
      }

      return styles
    }
  },

  methods: {
    /**
     * Returns the sized image URL.
     * @param {string} url - The original URL.
     * @param {number|string} width - The image width.
     * @param {string} format - The image format.
     * @returns {string}
     */
    getSizedImage(url, width, format) {
      if (url.includes('cdn.shopify.com')) {
        return getSizedImageUrl(url, `${width}x`)
      }

      return `${url}?w=${width}${format ? `&fm=${format}` : ''}`
    }
  }
}
</script>

<style lang="scss">
.responsive-image {
  $parent: &;
  background-color: $COLOR_BACKGROUND_LIGHT;
  display: block;
  overflow: hidden;
  position: relative;
  width: 100%;

  &__image {
    height: 100%;
    left: 0;
    object-fit: cover;
    position: absolute;
    top: 0;
    width: 100%;
  }

  &#{&}--contain {
    #{$parent}__image {
      object-fit: contain;
    }
  }

  &#{&}--fit {
    #{$parent}__image {
      object-fit: fit;
    }
  }

  &#{&}--fill {
    #{$parent}__image {
      object-fit: fill;
    }
  }
}
</style>
