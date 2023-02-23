<template>
  <v-row justify="start">
    <v-col 
      cols="12" 
      sm="6" 
      md="6"
      >
      <v-card
        class=""
        outlined
      >
        <v-list-item three-line>
          <v-list-item-content>
            <v-list-item-title class="text-h5 mb-1">
              {{ repola.title }}
            </v-list-item-title>
            <v-list-item-subtitle>{{ repola.description }}</v-list-item-subtitle>

            <div class="text-caption mt-4">
              {{ Date(repola.publisheddate) }}
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
      <v-card class="mt-4">
        <v-card-title>
          Tambah Event
        </v-card-title>
        <v-card-text>
          <v-text-field label="Nama Event" variant="outlined" v-model="eventTitle"></v-text-field>
          <v-textarea label="Deskripsi Event" variant="outlined" v-model="eventDescription"></v-textarea>
        </v-card-text>
        <v-card-actions>
          <v-btn
            outlined
            rounded
            text
            @click="submitEvent()"
          >
            Add
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
    <v-col cols="12" sm="6" md="6">
      <v-card
        v-for="list in eventLists"
        :key="list.id"
        class="mb-4 blue lighten-4"
        >
        <v-card-title>
          {{ list.data().title }}
        </v-card-title>
        <v-card-subtitle>
          {{ list.data().description }}
        </v-card-subtitle>
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
    slug: '',
    eventTitle: '',
    eventDescription: '',
    isSubmit: false,
    unsubscribe: null,
    unsubscribeEvents: null,
    eventLists: []
  }),
  methods: {
    submit: function() {
      this.isSubmit = true
    },
    getDocument: function(slug) {
      let self = this
      console.log(slug)
      this.unsubscribe = this.$fire.firestore.collection("reportase-langsung").doc(slug)
      this.unsubscribe.get()
        .then((doc) => {
          self.repola = doc.data()
        })
        .catch((error) => {
            console.log("Error getting documents: ", error)
        })
      // this.unsubscribe.onSnapshot({
      //       // Listen for document metadata changes
      //       includeMetadataChanges: true
      //   }, (doc) => {
      //       console.log(doc.data())
      //       self.repola = doc.data()
      //   });
        
    },
    getEvents: function(slug) {
      let self = this
      console.log(slug)
      self.unsubscribeEvents = this.$fire.firestore.collection("reportase-langsung")
        .doc(slug).collection("events").orderBy("publisheddate","desc").onSnapshot((querySnapshot) => {
        self.eventLists = []
        querySnapshot.forEach((doc) => {
          // doc.data() is never undefined for query doc snapshots  
          self.eventLists.push(doc)
        })
      });
    },
    submitEvent:  function() {
      let self = this
      let newData = this.$fire.firestore.collection('reportase-langsung').doc(this.slug).collection('events').add({
          title: self.eventTitle,
          description: self.eventDescription,
          publisheddate: self.$fireModule.firestore.Timestamp.now()
      })
      .then((docRef) => {
          console.log("Document written with ID: ", docRef.id);
      })
      .catch((error) => {
          console.error("Error adding document: ", error);
      });
    }
  },
  mounted() {
    this.slug = this.$route.query.id
    this.getDocument(this.slug)
    this.getEvents(this.slug)
  },
  beforeDestroy() {
    // console.log(this.unsubscribeEvents)
    if(this.unsubscribe){
      this.unsubscribe.onSnapshot()
    }
    if(this.unsubscribeEvents){
      this.unsubscribeEvents.onSnapshot()
    }
  }
}
</script>
