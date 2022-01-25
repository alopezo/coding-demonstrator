<template>
    <v-combobox
        v-model="binding.value"
        :items="items"
        :label="binding.title"
        :multiple="binding.isMultiple == true"
      ></v-combobox>
</template>
<script>
  import axios from "axios";

  export default {
    data: () => ({
        items: []
    }),
    props: {
      binding: {
        type: Object
      }
    },
    mounted() {
      var queryString = `https://browser.ihtsdotools.org/snowstorm/snomed-ct/MAIN/concepts?activeFilter=true&
                          termActive=true&language=en&offset=0&limit=50&ecl=${encodeURIComponent(this.binding.ecl)}`
      axios
        .get(queryString)
        .then(response => {
          this.items = response.data.items.map( e => e.fsn.term );
        })
    }
  }
</script>
