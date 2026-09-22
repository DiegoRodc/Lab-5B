<template>
 <v-app>
   <!-- Link to Material Icons-->
   <link
     href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Material+Icons"
     rel="stylesheet"
     type="text/css"
   />
  
   <!-- TODO: Add an AppBar component here -->
   <!-- Make sure to pass in the user object as a prop. -->


   <AppBar :user="user" />
  


   <v-main>
     <router-view :user="user"></router-view>
   </v-main>


 </v-app>
</template>


<script>
import AppBar from '@/components/AppBar.vue'

export default {
  name: 'App',
  components: { AppBar },
  data: () => ({
    user: {},
    defaultUser: { UserName: 'hello' },
    apiOrigin: process.env.VUE_APP_API_ORIGIN
  }),
  methods: {
    async getUser () {
      const url = `${this.apiOrigin}/api/v1/user` // <-- no trailing slash
      try {
        const res = await fetch(`${this.apiOrigin}/api/v1/user`, { credentials: 'include' })
        this.user = res.ok ? await res.json() : this.defaultUser
      } catch {
        this.user = this.defaultUser
      }
    }
  },
  mounted () { this.getUser() }
}
</script>