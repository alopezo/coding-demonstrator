<template>
  <v-container>
    <v-tabs>
      <v-tab v-for="(section, name) in sections" :key="name" :href="'#'+name">
        {{ section.title }}
      </v-tab>
      <v-tab-item v-for="(section, name) in sections" :key="name" :value="name">
        <SectionPanel :title="section.title" :note="section.note" :bindings="section.bindings"/>
      </v-tab-item>
    </v-tabs>
    <v-tabs-items>
    </v-tabs-items>
  </v-container>
</template>

<script>
  import SectionPanel from './SectionPanel.vue';


  export default {
    name: 'MainTabs',
    data: () => ({
    }),
    components: {
      SectionPanel
    },
    methods: {
    },
    props: {
      sections: Object
    }
  }
</script>
<style scoped>
  /* h1 {
    margin-top: 100px;
  } */
</style>