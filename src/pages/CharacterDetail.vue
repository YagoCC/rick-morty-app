<template>
  <q-page>
    <main class="detail-container" v-if="!isLoading">
      <figure class="card-img">
        <img :src="character.image" />
      </figure>
      <section class="card-info">
        <div class="main-info">
          <h1>Nome: {{ character.name }}</h1>
          <p>Status: {{ character.status }}</p>
          <p>Localização: {{ character.location.name }}</p>
          <p>Gênero: {{ character.gender }}</p>
        </div>

        <div class="card-episode">
          <h3>Episódios em que aparece:</h3>
          <ul class="episode-list">
            <li v-for="episode in character.episode" :key="episode.episode">
              - {{ episode.name }} ({{ episode.episode }})
            </li>
          </ul>
        </div>
      </section>
    </main>
  </q-page>
</template>

<script lang="ts">
import { CharacterInfo } from 'src/Interface';
import { defineComponent } from 'vue';
import { request } from 'graphql-request';

export default defineComponent({
  name: 'CharacterDetail',
  components: {},
  data() {
    return {
      character: new Array<CharacterInfo>(),
      isLoading: false,
    };
  },
  methods: {
    async loadCharacterDetail() {
      let query = `
        query {
          character (id: ${this.$route.params.id}) {
            id
            name
            image
            episode {
              episode
              name
            }
            status
            location {
              name
            }
            gender
          }
        }
      `;
      let data: { character: Array<CharacterInfo> } = await request(
        'https://rickandmortyapi.com/graphql',
        query
      );

      this.character = data.character;
    },
  },
  async created() {
    this.isLoading = true;
    await this.loadCharacterDetail();
    this.isLoading = false;
  },
});
</script>
<style lang="scss" scoped>
.detail-container {
  display: flex;
  flex-direction: column;
  margin: 15px 0;
  .main-info {
    display: flex;
    margin: 10px 0;
    flex-direction: column;
  }

  .card-episode {
    padding: 8px;
    border-radius: 4px;
    background: #00c3dd;
    margin: 10px 0;
    border: 1px solid $border-color;
  }
  h1,
  h3 {
    font-weight: 500;
    line-height: initial;
  }
  h1 {
    font-size: 22px;
  }

  h3 {
    font-size: 16px;
  }
  .episode-list {
    padding: 10px 0 0 15px;
    display: flex;
    flex-direction: column;
    row-gap: 7px;
  }
  .card-img {
    border-radius: 8px;
    overflow: hidden;
  }
}
@media only screen and (min-width: 768px) {
  .detail-container {
    flex-direction: row;
    column-gap: 5%;

    .card-img {
      flex: 30%;
    }
    .card-info {
      flex: 70%;
    }
  }
}
</style>
