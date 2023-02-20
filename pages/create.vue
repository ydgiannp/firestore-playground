<template>
  <v-row justify="start">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline">
          Buat Repola Baru
        </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="title"
            label="Judul Reportase Langsung"
            required
          ></v-text-field>
          <v-textarea 
            v-model="description"
            label="Deskripsi Reportase Langsung"
          ></v-textarea>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="blue lighten-2"
            block
            nuxt
            @click="submit"
          >
            Kirim
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
            :to="'show?id='+docId"
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
var slugify = require('slugify')
export default {
  name: 'CreatePage',
  data: () => ({
    title: '',
    description: '',
    isSubmit: false,
    docId: ''
  }),
  methods: {
    submit: function() {
      let slug = slugify(this.title)
      let newData = this.$fire.firestore.collection('reportase-langsung').doc(slug)
      let self = this
      newData.set({
        title: self.title,
        description: self.description,
        slug: slug,
        publisheddate: self.$fireModule.firestore.Timestamp.now()
      })
      .then(() => {
          self.docId = slug
          console.log("Document successfully written!");
      })
      .catch((error) => {
          console.error("Error writing document: ", error);
      });
      this.isSubmit = true
    }

  }
}
</script>
