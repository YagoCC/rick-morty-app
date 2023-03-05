<template>
  <q-page>
    <CharacterList />
  </q-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import CharacterList from 'components/CharacterList.vue';

export default defineComponent({
  name: 'IndexPage',
  components: {
    CharacterList,
  },
  data() {
    return {};
  },
});
</script>
