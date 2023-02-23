<template>
  <v-row justify="start">
    <v-col 
      cols="12" 
      sm="4" 
      md="4"
      v-if="lists.length"
      v-for="list in lists"
      :key="list.id"
      >
      <v-card
        class="mx-auto"
        max-width="344"
        outlined
      >
        <v-list-item three-line>
          <v-list-item-content>
            <div class="text-overline mb-4">
              {{ list.id }}
            </div>
            <v-list-item-title class="text-h5 mb-1">
              {{ list.data().title }}
            </v-list-item-title>
            <v-list-item-subtitle>{{ list.data().description }}</v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>

        <v-card-actions>
          <v-btn
            outlined
            rounded
            text
            :to="'/show?id='+list.id"
          >
            Show
          </v-btn>
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
            to="submit"
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
  name: 'IndexPage',
  data: () => ({
    title: '',
    description: '',
    isSubmit: false,
    lists: []
  }),
  methods: {
    submit: function() {
      this.isSubmit = true
    },
    getRepolaList: function() {
      let self = this
      var repolaRef = this.$fire.firestore.collection("reportase-langsung")
      // repolaRef.get()
      repolaRef.orderBy("publisheddate").get()
      // repolaRef.where("slug","==","pertama").orderBy("publisheddate").get()
        .then((querySnapshot) => {
            querySnapshot.forEach((doc) => {
                // doc.data() is never undefined for query doc snapshots
                console.log(doc.id, " => ", doc.data())
                self.lists.push(doc)
            })
        })
        .catch((error) => {
            console.log("Error getting documents: ", error)
        })
    }
  },
  mounted() {
    this.getRepolaList()
  }
}
</script>
