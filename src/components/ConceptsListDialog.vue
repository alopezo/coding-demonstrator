<template>
    <v-dialog
      v-model="dialog"
      scrollable
      max-width="800px"
    >
        <template v-slot:activator="{ on, attrs }">
            <v-btn
            color="success ml-4"
            dark
            v-bind="attrs"
            v-on="on"
            >
            Binding
            </v-btn>
        </template>

        <v-card>
            <v-card-title class="text-h5 grey lighten-2">
            {{ binding.title }}
            </v-card-title>

            <v-card-text style="height: 800px;">
                <v-subheader>ECL</v-subheader>
                <pre class="mx-4">{{ binding.ecl }}</pre>
                <v-list dense>
                    <v-subheader>MEMBERS  ({{ total }})</v-subheader>
                    <v-list-item-group
                        v-model="selectedItem"
                        color="primary"
                    >
                        <v-list-item
                        v-for="(item, i) in items"
                        :key="i"
                        >
                            <v-list-item-content>
                                <v-list-item-title v-text="item.fsn.term"></v-list-item-title>
                                <v-list-item-subtitle>SCTID: {{ item.id }}</v-list-item-subtitle>
                            </v-list-item-content>
                        </v-list-item>
                        <v-list-item v-if="total > items.length">
                            <v-list-item-content>
                                <v-list-item-subtitle class="text-center">Load more...</v-list-item-subtitle>
                            </v-list-item-content>
                        </v-list-item>
                        <v-list-item v-if="total == items.length">
                            <v-list-item-content>
                                <v-list-item-subtitle class="text-center">All results loaded ({{ total }})</v-list-item-subtitle>
                            </v-list-item-content>
                        </v-list-item>
                    </v-list-item-group>
                </v-list>
            </v-card-text>
            <v-divider></v-divider>
        </v-card>
    </v-dialog>
</template>

<script>
  import axios from "axios";

    export default {
        data: () => ({
            loading: false,
            items: [],
            total: 0,
            selectedItem: 0,
            dialog: false
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
                // this.items = response.data.items.map( e => e.fsn.term );
                this.items = response.data.items;
                this.total = response.data.total;
            })
        }
    }
</script>
