<template>
  <v-navigation-drawer v-model="drawer" absolute temporary>
    <v-list class="pa-1" v-if="user">
      <v-list-tile avatar>
        <v-list-tile-avatar>
          <img :src="user.photoURL">
        </v-list-tile-avatar>

        <v-list-tile-content>
          <v-list-tile-title>{{user.displayName}}</v-list-tile-title>
        </v-list-tile-content>
      </v-list-tile>
    </v-list>

    <v-list class="pt-0" dense>
      <v-divider></v-divider>

      <v-list-tile to="/Profile">
        <v-list-tile-action>
          <v-icon>dashboard</v-icon>
        </v-list-tile-action>

        <v-list-tile-content>
          <v-list-tile-title>Profile</v-list-tile-title>
        </v-list-tile-content>
      </v-list-tile>

      <v-list-tile @click="signOut">
        <v-list-tile-action>
          <v-icon>power_settings_new</v-icon>
        </v-list-tile-action>

        <v-list-tile-content>
          <v-list-tile-title>Sign Out</v-list-tile-title>
        </v-list-tile-content>
      </v-list-tile>
    </v-list>

    <v-list>
      <v-divider></v-divider>
      <v-subheader inset>History</v-subheader>

      <v-list-tile v-for="(lost_item,index) in lost_items" :key="`lost-${lost_item.type}-${index}`" @click="console.log('d')">
        <v-list-tile-content>
          <v-list-tile-title>{{ lost_item.type }}</v-list-tile-title>
        </v-list-tile-content>

        <v-list-tile-content>
          <v-list-tile-title>{{ lost_item.timestamp }}</v-list-tile-title>
        </v-list-tile-content>
      </v-list-tile>
      <v-divider inset></v-divider>
      <v-list-tile v-for="(found_item,index) in found_items" :key="`found-${found_item.type}-${index}`" @click="console.log('d')">
        <v-list-tile-content>
          <v-list-tile-title>{{ found_item.type }}</v-list-tile-title>
        </v-list-tile-content>

        <v-list-tile-content>
          <v-list-tile-title>{{ found_item.timestamp }}</v-list-tile-title>
        </v-list-tile-content>
      </v-list-tile>
    </v-list>
  </v-navigation-drawer>
</template>

<script>
import { EventBus } from '../../../main'
import { mapState } from 'vuex'

export default {
  props: ['mainDrawer'],
  computed: {
    ...mapState([
      'isUserLoggedIn',
      'user',
      'lost_items',
      'found_items',
      'firebase'
    ])
  },
  data () {
    return {
      drawer: this.mainDrawer
    }
  },
  methods: {
    toggleDrawer () {
      this.drawer = !this.drawer
      EventBus.$emit('toggleDrawer')
    },
    signOut () {
      this.firebase.auth().signOut().then(() => {
        // Sign-out successful.
        this.$store.dispatch('signOut')
      }).catch(function (error) {
        console.log(error)
      })
    }
  },
  watch: {
    mainDrawer () {
      this.drawer = this.mainDrawer
    },
    drawer () {
      console.log(this.drawer)
      if (!this.drawer) {
        this.toggleDrawer()
      }
    }
  }
}
</script>

<style>
</style>
