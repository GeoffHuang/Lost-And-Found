<template>
  <v-app id="inspire">
    <nav-bar></nav-bar>
    <v-content>
      <router-view/>
    </v-content>
  </v-app>
</template>

<script>
import Navbar from '@/components/layout/Navbar'
import { mapState } from 'vuex'

export default {
  components: {
    'nav-bar': Navbar
  },
  data () {
    return {}
  },
  computed: {
    ...mapState([
      'firebase',
      'db',
      'user'
    ])
  },
  created () {
    this.firebase.auth().onAuthStateChanged(
      (user) => {
        this.$store.dispatch('stillLoading', false)
        if (user) {
          // User is signed in.
          this.$store.dispatch('setUser', user)
          this.fetchAllUserDocuments()
        }
        this.fetchAllDocuments()
      }
    )

    this.db.collection('lost-items')
      .onSnapshot((querySnapshot) => {
        let lostItems = []
        querySnapshot.forEach(function (doc) {
          lostItems.push(doc.data())
        })
        this.$store.dispatch('setAllLostItems', lostItems)
        if (this.user) {
          this.$store.dispatch('updateUserCollection', 'lost-items')
        }
      })

    this.db.collection('found-items')
      .onSnapshot((querySnapshot) => {
        let foundItems = []
        querySnapshot.forEach(function (doc) {
          foundItems.push(doc.data())
        })
        this.$store.dispatch('setAllFoundItems', foundItems)
        if (this.user) {
          this.$store.dispatch('updateUserCollection', 'found-items')
        }
      })
  },
  methods: {
    // Get all documents by user from lost-items and found-items collection
    // and put it to lost_items and found_items array
    fetchAllUserDocuments () {
      this.$store.dispatch('updateUserCollection', 'lost-items')
      this.$store.dispatch('updateUserCollection', 'found-items')
    },
    fetchAllDocuments () {
      this.$store.dispatch('updateCollection', 'lost-items')
      this.$store.dispatch('updateCollection', 'found-items')
    }
  }
}
</script>
