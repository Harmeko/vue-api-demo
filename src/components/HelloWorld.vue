<template>
  <v-card
    color="red lighten-2"
    dark
  >
    <v-card-title class="headline red lighten-3">
      Search for Public APIs
    </v-card-title>
    <v-card-text>
      Explore hundreds of free API's ready for consumption! For more information visit
      <a
        class="grey--text text--lighten-3"
        href="https://github.com/toddmotto/public-apis"
        target="_blank"
      >the Github repository</a>.
    </v-card-text>
    <v-card-text>
      <v-text-field
        v-model="search"
        :loading="isLoading"
        color="white"
        label="Titre"
        placeholder="Start typing to Search"
      ></v-text-field>
    </v-card-text>
    <v-divider></v-divider>
    
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn
        :disabled="!model"
        color="grey darken-3"
        @click="model = null"
      >
        Clear
        <v-icon right>mdi-close-circle</v-icon>
      </v-btn>
    </v-card-actions>

    <v-card-text v-if="entries">
      <div v-for="entry in this.entries" :key="entry.id">
        <p>{{ entry.Title }}</p>
        <img v-bind:src="entry.Poster" alt="poster">
      </div>
    </v-card-text>
    
    <v-card-text v-if="length > 0">
      <v-pagination
        v-model="page"
        :page="page"
        :length="length"
        :total-visible="totalVisible"
      ></v-pagination>
    </v-card-text>
    
  </v-card>
</template>

<script>
  export default {
    data: () => ({
      descriptionLimit: 60,
      entries: [],
      isLoading: false,
      model: null,
      search: null,
      page: 1,
      length: 0,
      totalVisible: 10
    }),

    watch: {
      search (val) {
        if(val.length > 2){

          if (this.isLoading) return

          this.isLoading = true
          
          fetch('http://www.omdbapi.com/?apikey=8f0a5987&s=' + val + '&page=' + this.page)
            .then(res => res.json())
            .then(res => {
              const { totalResults, Search } = res
              this.length = Math.ceil(parseInt(totalResults) / 10)
              this.entries = Search
            })
            .catch(err => {
              console.log(err)
            })
            .finally(() => (this.isLoading = false))
          }
      },
      page (val) {
        if (this.isLoading) return

        this.isLoading = true

        fetch('http://www.omdbapi.com/?apikey=8f0a5987&s=' + this.search + '&page=' + val)
          .then(res => res.json())
          .then(res => {
            const { totalResults, Search } = res
            this.length = Math.ceil(parseInt(totalResults) / 10)
            this.entries = Search
          })
          .catch(err => {
            console.log(err)
          })
          .finally(() => (this.isLoading = false))
        }
    }
  }
</script>