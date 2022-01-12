<template>
  <b-container>
    <b-overlay :show="isBusy">
      <b-row>
        <b-col>
          <header>
            <img src="../assets/logo.png" alt="PokéDex" />
          </header>
        </b-col>
      </b-row>
      <b-row class="mt-4">
        <b-col
          md="4"
          sm="6"
          xs="12"
          v-for="(pokemon, index) in pokemonsList"
          :key="index"
        >
          <pokemon-card class="mb-4" :url="pokemon.url" />
        </b-col>
      </b-row>
      <b-row v-if="!isBusy">
        <b-col>
          <b-pagination
            v-model="currentPage"
            pills
            align="center"
            :total-rows="totalRows"
            :per-page="perPage"
            @change="changePage"
          />
        </b-col>
      </b-row>
    </b-overlay>
  </b-container>
</template>

<script>
import PokemonCard from "./PokemonCard.vue";

export default {
  name: "HelloWorld",
  components: {
    PokemonCard,
  },
  data() {
    return {
      pokemonsList: [],
      isBusy: false,
      totalRows: 0,
      currentPage: 1,
      perPage: 6,
      offSet: 0,
    };
  },
  mounted() {
    this.loadPokemonsList();
  },
  methods: {
    async loadPokemonsList() {
      this.pokemonsList = [];
      this.isBusy = true;

      await this.$http
        .get(`pokemon/?offset=${this.offSet}&limit=${this.perPage}`)
        .then(({ data }) => {
          this.totalRows = data.count;
          this.pokemonsList = data.results;
        })
        .catch((error) => {
          console.log(error);
        });

      this.isBusy = false;
    },
    changePage(page) {
      this.currentPage = page;
      this.offSet = this.currentPage * this.perPage - this.perPage;

      this.loadPokemonsList();
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  max-width: 850px;

  header {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 15px;

    img {
      width: 30%;

      @media (max-width: 768px) {
        width: 40%;
      }
      
      @media (max-width: 576px) {
        width: 50%;
      }
    }
  }
}
</style>
