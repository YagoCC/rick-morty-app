<template>
  <q-layout view="lHh Lpr lFf">
    <RouterLink to="/">
      <q-header elevated class="background-header row">
        <q-toolbar>
          <q-btn
            flat
            dense
            round
            icon="menu"
            aria-label="Menu"
            @click="toggleLeftDrawer"
          />

          <q-toolbar-title> Rick & Morty Project </q-toolbar-title>
        </q-toolbar>
      </q-header>
    </RouterLink>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      :width="300"
      :breakpoint="992"
    >
      <q-scroll-area
        style="
          height: calc(100% - 150px);
          margin-top: 150px;
          border-right: 1px solid #ddd;
        "
      >
        <q-list padding>
          <q-item-label header>Links Sociais</q-item-label>

          <EssentialLink
            v-for="link in essentialLinks"
            :key="link.title"
            v-bind="link"
          />
        </q-list>
      </q-scroll-area>

      <q-img
        class="absolute-top"
        src="https://cdn.quasar.dev/img/material.png"
        style="height: 150px"
      >
        <div class="absolute-bottom bg-transparent">
          <q-avatar size="56px" class="q-mb-sm">
            <img
              src="https://media.licdn.com/dms/image/D4D03AQHNWB-k5aMBiA/profile-displayphoto-shrink_800_800/0/1676225390842?e=1683763200&v=beta&t=0VIzhK9oTujOBdJ4jKfARJGyWx3SXYpkVOhR8QOIWQ8"
            />
          </q-avatar>
          <div class="text-weight-bold">Yago Cordeiro</div>
          <a
            href="https://www.linkedin.com/in/yago-cordeiro-front/"
            target="_blank"
          >
            @yago-cordeiro-front
          </a>
        </div>
      </q-img>
    </q-drawer>

    <q-page-container class="default-content">
      <router-view />
      <!-- place QPageScroller at end of page -->
      <q-page-scroller
        position="bottom-right"
        :scroll-offset="150"
        :offset="[18, 18]"
      >
        <q-btn fab icon="keyboard_arrow_up" style="background: #00c3dd" />
      </q-page-scroller>
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import EssentialLink from 'components/EssentialLink.vue';

const linksList = [
  {
    title: 'Github',
    caption: 'github.com/YagoCC',
    icon: 'code',
    link: 'https://github.com/YagoCC',
  },
];

export default defineComponent({
  name: 'MainLayout',

  components: {
    EssentialLink,
  },

  data() {
    return {
      leftDrawerOpen: false,
      essentialLinks: linksList,
    };
  },

  methods: {
    toggleLeftDrawer() {
      this.leftDrawerOpen = !this.leftDrawerOpen;
    },
  },
});
</script>

<style lang="scss" scoped>
.default-content {
  max-width: 90%;
  margin: 0 auto 40px;
  cursor: default;
}
:deep {
  .background-header {
    height: 70px;
    background: linear-gradient(
      90deg,
      rgba(11, 194, 75, 0.8) 0%,
      rgba(9, 115, 121, 0.8) 35%,
      rgba(0, 212, 255, 0.8) 100%
    );
  }
}
</style>
