<template>
  <div class="flex-container">
    <BookItem v-for="book in books"
              :key="book.id"
              :book="book" 
              :showAddBookmark="showAddBookmark"
              :showTrashcan="showTrashcan"
              @bookAdded="onBookAdded(book)"
              @bookDeleted="onBookDeleted(book)"/>
  </div>
</template>

<script>
import BookItem from "./BookItem"

export default {
  props: {
    books: {
      type: Array,
      required: true
    },
    showTrashcan: {
      type: Boolean,
    },
    showAddBookmark: {
      type: Boolean,
    }
  },
  methods: {
    onBookAdded(book) {
      this.$emit('bookAdded', book)
    },
    onBookDeleted(book) {
      this.$emit('bookDeleted', book)
    },
  },
  components: {
    BookItem
  }
}
</script>

<style lang="scss">
@import '@/styles/style.scss';
</style>