<template>
   <div id='app-bar'>
     <v-app-bar app color="primary" dark>
        <v-toolbar-title>Todo App</v-toolbar-title>
        <v-spacer></v-spacer>
        <!-- TODO: Use a v-if to only show this button when the user is logged in -->
       <!-- <v-btn icon v-on:click.stop="drawer = !drawer"> -->
       <v-btn v-if="loggedIn" icon @click.stop="drawer = !drawer">
         <v-icon>mdi-menu</v-icon>
       </v-btn>
     </v-app-bar>
    <v-navigation-drawer v-model="drawer" absolute temporary right>
      <!-- User header -->
      <v-list-item
        :prepend-avatar="user.PhotoURL || user.photoUrl || user.picture || ''"
      >
        <v-list-item-title>
          {{ user.UserName || user.userName || 'User' }}
        </v-list-item-title>
      </v-list-item>

      <v-divider></v-divider>

      <!-- Logout -->
      <v-list dense>
        <v-list-item :href="`${apiOrigin}/api/v1/auth/logout?returnTo=http://localhost:8081/login`" link>
          <v-icon>mdi-logout-variant</v-icon>
          <v-list-item-title>Log Out</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>



   </div>
 </template>
  <script>
 import { authenticated } from '@/util'
 export default {
    name: 'AppBar',
   props: {
     // TODO: Pass in the user object to this component as a prop >>
     // Make sure to give it a default value that has the UserName defined as an empty string
     user: {
       type: Object,
       default: () => ({ UserName: '' })
     }
   },
   data: () => ({
     drawer: false,
     loggedIn: false,
     // TODO: Get the api's url origin from process.env and set it to a variable here >>
     apiOrigin: process.env.VUE_APP_API_ORIGIN
   }
   ),
   methods: {
     async getLoggedIn(){
       try {
     const res = await fetch(`${this.apiOrigin}/api/v1/user`, { credentials: 'include' })

     this.loggedIn = res.ok
     // optional: console.log('loggedIn =', this.loggedIn)
   } catch (e) {
     this.loggedIn = false
   }


   }
   },
  mounted(){
       this.getLoggedIn()
  }
  }
 </script>