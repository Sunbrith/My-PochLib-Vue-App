<template>
  <div id="app">
    <div>
      <img id="logo" src="./assets/pochlib_logo.png" alt="Logo">
    </div>

    <div>
      <h1>Nouveau livre </h1>
      <div>
        <button id="add-book-btn" @click="showForm = !showForm" depressed rounded dark>Ajouter un livre</button>
      </div>
      <br>
      <br>
      <template v-if="showForm">
        <div class="query">
          <form @submit.prevent="search">
            <div>
              <h3>Titre du livre</h3>
              <input v-model="titleSearch">
              <h3>Auteur</h3>
              <input v-model="authorSearch">
              <br>
              <br>
              <button id="search-btn">Rechercher</button>
              <br>
              <button @click="clearSearch" type="button" id="clear-search-btn" depressed rounded dark>Annuler</button>
            </div>
          </form>
        </div>
        <div class="content">
          <div class="loading" v-if="loadState == 'loading'"></div>
          <br>
          <div v-if="loadState == 'success'"> <h1>Résultats de recherche</h1> </div> 
          <BookList v-if="loadState == 'success'"
                    :books="books"
                    :showAddBookmark="true"
                    @bookAdded="addToMyPochList"/>
        </div>
      </template>
    </div>
    <br>
    <div>
      <h1>Ma Poch'liste</h1>
      <BookList :books="savedBooks"
                :showTrashcan="true"
                @bookDeleted="removeFromPochListe"/>
    </div>
  </div>
</template>

<script>
import BookList from '@/components/BookList'
import axios from 'axios'

export default {
  data() {
    return {
      showForm: false,
      books: [],
      savedBooks: [],
      titleSearch: '',
      authorSearch: '',
      orderBy: 'relevance',
      maxResults: '10',
      loadState: '',
    }
  },
 
  methods: {

    search() {
      this.loadState = 'loading'
      let keyword = ''
      if (this.titleSearch !== undefined && this.titleSearch !== '') {
        keyword = this.titleSearch
        if (this.authorSearch !== undefined && this.authorSearch !== '') {
          keyword += `+inauthor:${this.authorSearch}`
        }
      } else {
        keyword = this.authorSearch
      }
      axios
        .get(
          `https://www.googleapis.com/books/v1/volumes?q=${
            keyword
          }&orderBy=${this.orderBy}&maxResults=${this.maxResults}`
        )
        .then(response => {
          if (this.response == '') {
            this.$alert("Aucun livre n’a été trouvé");  
          }
          else
            {
          this.books = response.data.items
          this.loadState = 'success'
          }
        })
    },

    clearSearch() {
      this.titleSearch = ''
      this.authorSearch = ''
      this.loadState = ''
      this.books = []
      this.showForm = ''
    },

    addToMyPochList(book) {

      if (!sessionStorage.getItem('savedBooks')) {
        sessionStorage.setItem('savedBooks', '[]')
      }

      const currentSavedBooks = sessionStorage.getItem('savedBooks')

      const currentSavedBooksAsJSON = JSON.parse(currentSavedBooks)

      if (currentSavedBooksAsJSON.findIndex(b => b.id === book.id) > -1) {
        alert("Vous ne pouvez ajouter deux fois le même livre")
        return
      }

      currentSavedBooksAsJSON.push(book)

      const savedBooksAsString = JSON.stringify(currentSavedBooksAsJSON)
      sessionStorage.setItem('savedBooks', savedBooksAsString)

      this.loadPochListe()
    },

    removeFromPochListe(book) {
      if (!sessionStorage.getItem('savedBooks')) {
        return
      }

      const currentSavedBooks = sessionStorage.getItem('savedBooks')

      const currentSavedBooksAsJSON = JSON.parse(currentSavedBooks)

      const index = currentSavedBooksAsJSON.findIndex(b => b.id === book.id)
      
      if (index > -1) {
        currentSavedBooksAsJSON.splice(index, 1)

        const savedBooksAsString = JSON.stringify(currentSavedBooksAsJSON)

        sessionStorage.setItem('savedBooks', savedBooksAsString)

        this.loadPochListe()
      }
    }, 

    loadPochListe() {
      let savedBooks = []

      if (sessionStorage.getItem('savedBooks')) {
        savedBooks = JSON.parse(sessionStorage.getItem('savedBooks'))
      }

      this.savedBooks = savedBooks
    }
  },
  mounted() {
    this.loadPochListe()
  },
  components: {
    BookList,
  }
}
</script>

<style lang="scss">
@import '@/styles/style.scss';
</style>