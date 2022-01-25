<template>
    <v-autocomplete
        v-model="binding.value"
        :label="binding.title"
        :items="items"
        :loading="loading"
        :search-input.sync="search"
        no-data-text="Type at least 3 characters to search..."
        cache-items
        clearable
    ></v-autocomplete>
</template>
<script>
  import axios from "axios";

  export default {
    data: () => ({
        search: null,
        loading: false,
        items: []
    }),
    props: {
      binding: {
        type: Object
      }
    },
    watch: {
      search (val) {
        if (this.timeout) 
          clearTimeout(this.timeout); 

        this.timeout = setTimeout(() => {
          val && val != this.binding.value && val.length > 2 && this.querySelections(val)
        }, 300); // delay
      },
    },
    methods: {
      querySelections (v) {
        this.loading = true;
        var queryString = `https://browser.ihtsdotools.org/snowstorm/snomed-ct/MAIN/concepts?activeFilter=true&term=${v}&
                            termActive=true&language=en&offset=0&limit=50&ecl=${encodeURIComponent(this.binding.ecl)}`
        axios
          .get(queryString)
          .then(response => {
            this.items = response.data.items.map( e => e.fsn.term );
            this.loading = false;
          })
      }
    }
  }
</script>
