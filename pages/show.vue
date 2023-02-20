<template>
  <v-row justify="start">
    <v-col 
      cols="12" 
      sm="4" 
      md="4"
      >
      <v-card
        class="mx-auto"
        max-width="344"
        outlined
      >
        <v-list-item three-line>
          <v-list-item-content>
            <v-list-item-title class="text-h5 mb-1">
              {{ repola.title }}
            </v-list-item-title>
            <v-list-item-subtitle>{{ repola.description }}</v-list-item-subtitle>

            <div class="text-caption mt-4">
              {{ Date(repola.publishedDate) }}
            </div>
          </v-list-item-content>
        </v-list-item>

        <v-card-actions>
          <!-- <v-btn
            outlined
            rounded
            text
          >
            Show
          </v-btn> -->
        </v-card-actions>
      </v-card>
    </v-col>
    <v-col cols="12" sm="8" md="6" v-if="isSubmit">
      <v-card
        class="mx-auto light--text"
        prepend-icon="mdi-twitter"
        title="Twitter"
        outlined
      >
        <template v-slot:prepend>
          <v-icon size="x-large"></v-icon>
        </template>

        <v-card-text class="py-2">
          <v-icon color="green">mdi-check-decagram</v-icon>
          Repola berhasil masuk ke firestore
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn
            color="blue lighten-2"
            nuxt
            @click="submit"
            block
          >
            Lihat Detail
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'showPage',
  data: () => ({
    repola: {
      title: '',
      description: '',
      date: ''
    },
    isSubmit: false,
  }),
  methods: {
    submit: function() {
      this.isSubmit = true
    },
    getDocument: function(slug) {
      let self = this
      console.log(slug)
      var docRef = this.$fire.firestore.collection("reportase-langsung").doc(slug)
      // docRef.get()
      //   .then((doc) => {
      //     self.repola = doc.data()
      //   })
      //   .catch((error) => {
      //       console.log("Error getting documents: ", error)
      //   })
      docRef.onSnapshot({
            // Listen for document metadata changes
            includeMetadataChanges: true
        }, (doc) => {
            console.log(doc.data())
            self.repola = doc.data()
        });
    }
  },
  mounted() {
    this.getDocument(this.$route.query.id)
  }
}
</script>
