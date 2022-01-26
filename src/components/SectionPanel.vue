<template>
  <v-form v-model="valid">
    <v-container>
      <v-row>
        <h3 class="mt-4">{{ title }}</h3>
      </v-row>
      <v-row>
        <p class="mt-4">{{ note }}</p>
      </v-row>
      <v-row v-for="(binding, name) in bindings" :key="name" class="mb-4">
        <v-col cols="6" md="6">
          <h4>{{ binding.title }}</h4>
          <p class="body-1 font-weight-light" v-html="addLinks(binding.note)"></p>
        </v-col>
        <v-col cols="6" md="6" class="text-right">
          <v-chip class="text-capitalize" color="amber darken-1" text-color="white">{{ binding.type }}</v-chip>
          <ConceptsListDialog :binding='binding' />
          <AutocompleteBinding :binding='binding' v-if="binding.type == 'autocomplete'"/>
          <DropdownBinding :binding='binding' v-if="binding.type == 'dropdown'"/>
          <RadiobuttonBinding :binding='binding' v-if="binding.type == 'radiobutton'"/>
        </v-col>
      </v-row>
    </v-container>
  </v-form>
</template>

<script>
  import AutocompleteBinding from './AutocompleteBinding.vue'
  import DropdownBinding from './DropdownBinding.vue'
  import RadiobuttonBinding from './RadiobuttonBinding.vue'
  import ConceptsListDialog from './ConceptsListDialog.vue'

  export default {
    components: {
      AutocompleteBinding,
      DropdownBinding,
      RadiobuttonBinding,
      ConceptsListDialog
    },
    props: {
      title: String,
      note: String,
      bindings: Object
    },
    data: () => ({
      valid: false,
      search: null,
      loading: false,
      items: [],
    }),
    methods: {
      addLinks(text) {
        if (text) {
          var references = text.match(/(\d*\s\|.*?\|)/gm)
          if (references) {
            for (const reference of references) {
              var code = reference.match(/(\d*)/)[0]
              var link = '<a href="http://snomed.info/sct/' + code + '" target="_blank">' + reference + '</a>'
              text = text.replace(reference, link)
            }
          }
        }
        return text;
      }
    }
  }
</script>