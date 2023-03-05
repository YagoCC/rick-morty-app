<template>
  <div class="character-page">
    <q-input
      v-model="searchQuery"
      debounce="500"
      outlined
      placeholder="Pesquise pelo nome"
      @clear="clearFilter()"
      @update:model-value="filterCharacters()"
      clearable
      clear-icon="close"
    />
    <div>
      <ul class="character-grid" v-if="!!characters.length">
        <li v-for="character in characters" :key="character.id">
          <div class="card-character" @click="goToDetail(character.id)">
            <q-tooltip
              anchor="top middle"
              self="bottom middle"
              :offset="[10, 10]"
            >
              <strong>Clique para mais detalhes!</strong>
            </q-tooltip>
            <div class="card-inner">
              <div class="card-front">
                <p class="card-name">{{ character.name }}</p>
                <img
                  class="card-img"
                  :src="character.image"
                  @load="imageLoaded()"
                />
              </div>
              <div class="card-back">
                <p>Nome: {{ character.name }}</p>
                <p>Status: {{ character.status }}</p>
                <p>Localização: {{ character.location.name }}</p>
              </div>
            </div>
          </div>
        </li>
      </ul>
      <div v-else-if="!isLoading">
        Não existe nenhum personagem com o nome pesquisado.
      </div>
    </div>

    <div class="load-section" v-if="loaded && !isFiltering">
      <ScrollInfinite @endOfScroll="loadCharacters()" />
    </div>
  </div>
</template>

<script lang="ts">
import { CharacterInfo } from 'src/Interface';
import { defineComponent } from 'vue';
import { request } from 'graphql-request';
import ScrollInfinite from 'components/ScrollInfinite.vue';

export default defineComponent({
  data() {
    return {
      characters: new Array<CharacterInfo>(),
      charactersTemp: new Array<CharacterInfo>(),
      loaded: false,
      countImagesLoaded: 0,
      imagesOnPage: 0,
      page: 1,
      searchQuery: '',
      isFiltering: false,
      isLoading: false,
    };
  },
  components: {
    ScrollInfinite,
  },
  async mounted() {
    this.isLoading = true;
    await this.loadCharacters();
    this.charactersTemp = this.characters;
    this.isLoading = false;
  },
  methods: {
    goToDetail(id: string) {
      this.$router.push({ path: `/personagem/${id}` });
    },
    clearFilter() {
      this.isFiltering = false;
      this.searchQuery = '';
      this.characters = this.charactersTemp;
    },
    imageLoaded() {
      this.countImagesLoaded++;

      if (this.countImagesLoaded === this.imagesOnPage) this.loaded = true;
    },
    async filterCharacters() {
      if (this.searchQuery === '') {
        this.clearFilter();
        return;
      }
      this.isFiltering = true;
      await this.loadCharacters();
    },
    async loadCharacters() {
      let query = `
        query {
          characters (page: ${
            this.isFiltering ? 1 : this.page
          }, filter: {name: "${this.searchQuery}"}) {
            results {
              id
              name
              location {
                name
              }
              gender
              status
              image
            }
          }
        }
      `;
      let data: { characters: { results: Array<CharacterInfo> } } =
        await request('https://rickandmortyapi.com/graphql', query);

      if (this.isFiltering) {
        this.characters = data.characters.results;
      } else {
        this.charactersTemp = this.characters;
        this.page++;
        this.imagesOnPage += 20;
        this.characters.push(...data.characters.results);
      }
    },
  },
});
</script>

<style lang="scss" scoped>
.load-section {
  margin: 10px 0;
}
.character-page {
  display: flex;
  flex-direction: column;
  row-gap: 20px;
  margin-top: 40px;
}
.character-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 25px;
}
.card-character {
  display: flex;
  flex-direction: column;
  cursor: pointer;
  object-fit: cover;

  .card-img {
    min-height: 200px;
  }
  .card-name {
    line-height: 24px;
    padding: 0 12px;
    background: rgba(0, 247, 167);
    position: absolute;
    font-weight: 500;
    border: 1px solid black;
    border-left: none;
    bottom: 10px;
  }
  .card-inner {
    position: relative;
    width: 100%;
    height: 100%;
    text-align: center;
    transition: transform 0.8s;
    transform-style: preserve-3d;
    border-radius: 8px;
  }

  .card-front,
  .card-back {
    width: 100%;
    height: 100%;
    -webkit-backface-visibility: hidden; /* Safari */
    backface-visibility: hidden;
  }
  .card-front {
    background-color: #bbb;
    color: black;
  }

  /* Style the back side */
  .card-back {
    padding: 10px;
    text-align: left;
    display: flex;
    flex-direction: column;
    position: absolute;
    top: 0;
    background-color: #00c3dd;
    color: white;
    transform: rotateY(180deg);
  }
}

@media only screen and (min-width: 528px) {
  .character-grid {
    grid-template-columns: 1fr 1fr;
  }
}

@media only screen and (min-width: 768px) {
  .character-grid {
    grid-template-columns: 1fr 1fr 1fr;
  }
}

@media only screen and (min-width: 992px) {
  .character-grid {
    grid-template-columns: 1fr 1fr 1fr 1fr;
  }

  .card-character {
    &:hover .card-inner {
      transform: rotateY(180deg);
    }
  }
}

@media only screen and (min-width: 1366px) {
  .character-grid {
    grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  }
}
</style>
