<template>
  <b-overlay :show="isBusy">
    <b-card :style="styleObject(true, pokemon.primaryType)">
      <b-card-body>
        <div class="circle">
          <b-card-img :src="pokemon.img"></b-card-img>
        </div>
        <b-card-title class="mb-0 text-capitalize" :title="pokemon.name" />
        <b-card-sub-title> Nº {{ pokemon.id }} </b-card-sub-title>
        <b-card-text>
          <b-badge
            v-for="(type, index) in pokemon.types"
            class="text-capitalize mr-1 px-3 pt-2 pb-2"
            :style="styleObject(false, type.name)"
            :key="index"
          >
            {{ type.name }}
          </b-badge>
        </b-card-text>
      </b-card-body>
    </b-card>
  </b-overlay>
</template>

<script>
import { getColorType } from "../context";

export default {
  name: "PokemonCard",
  props: {
    url: String,
  },
  data() {
    return {
      pokemon: {},
      isBusy: false,
    };
  },
  async mounted() {
    this.pokemon = {};
    this.isBusy = true;

    const parts = this.url.split("/");
    const id = parts.pop() || parts.pop();

    this.pokemon = await this.$http.get(`pokemon/${id}`).then(({ data }) => ({
      id: data.id,
      name: data.name,
      img: data.sprites.front_default,
      types: data.types.map(({ type }) => type),
      primaryType: data.types[0].type.name,
    }));

    this.isBusy = false;
  },
  methods: {
    styleObject(card, type) {
      if (!type) return;

      const { textColor, bgColor, bgCardColor } = getColorType(type);

      if (card) {
        return {
          "background-color": bgCardColor,
        };
      } else {
        return {
          color: textColor,
          "background-color": bgColor,
        };
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.card {
  cursor: pointer;

  &:hover {
    box-shadow: 0 0 1em #e4e4e4;
  }

  .card-body {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 15px;

    .card-title {
      font-size: 20px;
    }

    .card-subtitle {
      font-size: 14px;
      margin: 10px 0 13px 0;
    }

    .circle {
      position: relative;
      height: 150px;
      width: 150px;
      background-color: #fafafa;
      border-radius: 50%;
      margin-bottom: 15px;
    }
  }
}
</style>