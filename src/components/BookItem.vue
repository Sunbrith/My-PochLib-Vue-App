<template>
  <div class="book">
    <div v-if="showAddBookmark">
      <img @click="onAdd"
           src="../assets/bookmark-regular.svg"
           alt="bookmark"
           class="actionIcon">
    </div>
    <div v-if="showTrashcan">
      <img @click="onRemove"
           src="../assets/trash-alt-regular.svg"
           alt="trashcan"
           class="actionIcon">
    </div>
    <div>
      <div class="details">
        <h1>Titre: {{ info.title }}</h1>
        <h2 class="author">
          <span v-if="!info.authors">Information manquante</span>
          <span v-else > Auteur / Autrice: {{ info.authors[0] }} </span>
        </h2>
        <p> ID: {{ book.id }}</p>
        <h3 class="description">
          <p>
          <span v-if="!info.description">Information manquante</span>
          <span v-else>Description: {{ info.description.substring(0, 200) }}</span>
          </p>
        </h3>
      </div>
      <div class="thumbnail">
        <img v-if= "info.imageLinks"
            :src= "info.imageLinks.thumbnail"
            :alt="info.title">
        <img v-else
            src="https://s3-eu-west-1.amazonaws.com/course.oc-static.com/projects/Salesforce_P1_FR/unavailable.png"
            alt= "Unavailable">
      </div>
    </div>
  </div>
</template>

<script>
export default {
   props: {
    book: {
      type: Object,
    },
    showAddBookmark: {
      type: Boolean,
    },
    showTrashcan: {
      type: Boolean,
    }
  },
  methods: {
    onAdd() {
      this.$emit('bookAdded')
    },
    onRemove(){
      this.$emit('bookDeleted')
    }
  },
  computed: {
    info(){
      return this.book.volumeInfo
    }
  }
}
</script>

<style lang="scss">
@import '@/styles/style.scss';
</style>