<!--
  A token is one piece of text, such as a word or punctuation.
  A token block groups tokens into an annotation.
-->
<template>
  <span
    v-if="token.type === 'token'"
    :id="'t' + token.start"
    class="token"
    :style="tokenStyle"
  >
    {{ token.text == ' ' ? '&nbsp;' : token.text }}
  </span>
</template>

<script>
export default {
  name: 'Token',
  props: {
    // The token contains its text and its position in the original text.
    token: {
      type: Object,
      required: true,
    },
  },
  computed: {
    tokenStyle() {
      // Get the original text of the current paragraph from the Vuex store.
      const state = this.$store?.state
      const text = state?.annotationManager?.inputSentences?.[state.currentIndex]?.text

      // Keep the existing padding unless a rule below removes it.
      const style = { padding: '0.25rem' }

      // If the original text is unavailable, keep the default padding.
      if (typeof text !== 'string') return style

      // token.start is the position of this token's first character.
      // Check the character just before it.
      // \S means a character that is NOT whitespace.
      // If there is no whitespace before this token, remove its left padding.
      // Keep the first token's left padding so the first line stays aligned.
      if (
        this.token.start > 0 &&
        /\S/u.test(text[this.token.start - 1])
      ) {
        style.paddingLeft = '0'
      }

      // token.end is the position just AFTER this token's last character.
      // Remove right padding if the next character is not whitespace,
      // or if this token ends at the end of the original text.
      if (
        this.token.end === text.length ||
        /\S/u.test(text[this.token.end])
      ) {
        style.paddingRight = '0'
      }

      // Example: "Hello," stays together without an extra visual gap.
      // Example: "Hello ," keeps the existing padding around the space.
      // These rules control visual padding, not the exact number of spaces.
      return style
    },
  },
}
</script>

<style lang="scss">
.token {
  /* Keep the existing text size. */
  font-size: large;

  /* Allow each token to have its own padding. */
  display: inline-block;

  /* Keep the existing line spacing. */
  line-height: 3;
}
</style>
