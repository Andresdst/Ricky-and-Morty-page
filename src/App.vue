<template>
  <div id="app">
    <div class="hero is-white is-gradient is-bold">
      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-success">Rick & Morty</span>
          <span class="subtitle">Personajes</span>
        </h1>

        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
              v-model="search"
              type="text"
              class="input is-rounded"
              v-on:keyup.enter="searchData"
            >
          </div>

          <div class="control">
            <button class="button is-success is-rounded" v-on:click="searchData">buscar</button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div class="columns is-desktop is-mobile is-tablet is-multiline is-centered">
        <character
          @showModal="showModal"
          v-for="character of characters"
          v-bind:key="character.id"
          v-bind:character="character"
        ></character>
      </div>

      <nav class="pagination" role="navigation" aria-label="pagination">
        <a class="pagination-previous" v-on:click="changePage(page - 1)">Anterior</a>

        <ul class="pagination-list">
          <li>
            <a href class="pagination-link is-current">{{ page }}</a>
          </li>
        </ul>

        <a class="pagination-next" v-on:click="changePage(page + 1)">Siguiente</a>
      </nav>
    </div>

    <div class="modal" :class="{'is-active':modal}" v-if="modal">
      <div class="modal-background" @click="modal=false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Acerca de: {{ charSelected.name }}</p>
        </header>

        <div class="modal-card-body">
          <strong>Genero:</strong>
          <p>{{charSelected.gender}}</p>
          <strong>Estado:</strong>
          <p>{{charSelected.status}}</p>
          <strong>Especie:</strong>
          <p>{{charSelected.species}}</p>
          <strong>Tipo:</strong>
          <p>{{charSelected.type}}</p>
        </div>

        <footer class="modal-card-foot">
          <button class="button" @click="modal=false">Cerrar</button>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
//librerias
import axios from "axios";
import Character from "./components/Character";

export default {
  name: "App",
  components: {
    Character
  },

  data() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      charSelected: {}
    };
  },

  created() {
    this.fetch();
  },

  methods: {
    showModal(id) {
      this.fetchOne(id);
    },

    async fetchOne(id) {
      let result = await axios.get(
        `https://rickandmortyapi.com/api/character/${id}/`
      );
      this.charSelected = result.data;
      this.modal = true;
      console.log(this.charSelected);
    },

    searchData() {
      this.page = 1;
      this.fetch();
    },

    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.fetch();
    },

    fetch() {
      const params = {
        page: this.page,
        name: this.search
      };
      let result = axios
        .get("https://rickandmortyapi.com/api/character/", { params })
        .then(res => {
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
          console.log(res.data);
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>

<style>
#app {
}
</style>
