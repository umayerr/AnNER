<template>
  <div class="q-pa-md" style="">
    <div style="display: inline-block">
      <!-- Show the live count received from the editor page. -->
      <span class="q-pl-md">
        Annotations: <strong>{{ annotationCount }}</strong>
      </span>

      <span class="q-pl-md">
        Paragraph: <strong>{{ currentIndex + 1 }}/{{ annotationManager.annotations.length }}</strong>
      </span>

      <span class="q-pl-md">
        Paragraph ID: <strong>{{ annotationManager.annotations[currentIndex].id ?? 'None' }}</strong>
      </span>
    </div>
    <q-btn
      class="q-mx-sm"
      :color="$q.dark.isActive ? 'grey-3' : 'grey-9'"
      outline
      title="Go back one sentence/paragraph"
      @click="back"
      :disabled="currentIndex == 0"
      label="Back"
      style="position: absolute; right: 95px; bottom: 17px"
    />
    <q-btn
      class="q-mx-sm"
      :color="$q.dark.isActive ? 'grey-3' : 'grey-9'"
      outline
      title="Go forward one sentence/paragraph"
      @click="next"
      :disabled="currentIndex == this.annotationManager.inputSentences.length - 1"
      label="Next"
      style="position: absolute; right: 16px; bottom: 17px"
    />
  </div>
</template>

<script>
import { mapState, mapMutations } from 'vuex'

export default {
  name: 'InfoBar',
  props: {
  // Receive the visible annotation count from the parent page.
  // Show 0 if the parent does not provide a count.
    annotationCount: {
      type: Number,
      default: 0,
    },
  },
  computed: {
    ...mapState(['currentIndex', 'annotationManager']),
  },
  methods: {
    ...mapMutations(['setCurrentIndex', 'setCurrentPage', 'nextSentence', 'previousSentence']),
    getWordCount(text) {
      if (text == null) return 0
      const words = text.split(/\s+/).filter((word) => word.length > 0)
      return words.length
    },
    getCharCount(text) {
      if (text == null) return 0
      return text.length
    },
    next() {
      this.nextSentence()
      this.emitter.emit('tokenizeCurrentSentence')
    },
    back() {
      this.previousSentence()
      this.emitter.emit('tokenizeCurrentSentence')
    },
  },
}
</script>
