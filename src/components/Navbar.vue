
<template>
    <nav>
        <v-toolbar app dark>
            <v-toolbar-side-icon @click="drawer = !drawer"></v-toolbar-side-icon>
            <v-toolbar-title class="font-weight-light">A HOME TO SHARE</v-toolbar-title>
            <v-toolbar-title>ADMIN CONSOLE</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn flat @click="signOut">
                <span>Sign out</span>
                <v-icon right>exit_to_app</v-icon>
            </v-btn>
        </v-toolbar>
        <v-navigation-drawer app v-model="drawer" dark>
            <v-toolbar flat>
            <v-list>
                <v-list-tile>
                <v-list-tile-title class="title">
                    CONSOLE MENU
                </v-list-tile-title>
                </v-list-tile>
            </v-list>
            </v-toolbar>

            

            <v-list class="pt-0">
            <v-list-tile v-for="item in items" :key="item.title" router :to="item.route">
                <v-list-tile-action>
                <v-icon class="white--text">{{ item.icon }}</v-icon>
                </v-list-tile-action>
                <v-list-tile-content>
                <v-list-tile-title class="white--text">{{ item.title }}</v-list-tile-title>
                </v-list-tile-content>
            </v-list-tile>
            </v-list>
        </v-navigation-drawer>
    </nav>
</template>

<script>
  import store from "../store/store";
  import * as types from "../store/types";
  import router from "../router";


  export default {
    data(){
        return{
            
            items: [
                { title: 'DASHBOARD', icon: 'dashboard', route:'/dashboard' },
                { title: 'HOST MANAGEMENT', icon: 'accessibility_new', route:'/hostManagement' },
                { title: 'RENTER MANAGEMENT', icon: 'face', route:'/renterManagement' },
                { title: 'HOME REQUEST MANAGEMENT', icon: 'swap_horiz', route:'/homeRequestManagement' },
                { title: 'ARTICLE MANAGEMENT', icon: 'library_books', route:'/articleManagement' },
                { title: 'EDIT ACCOUNT', icon: 'person', route:'/editAccount' },
                { title: 'ABOUT', icon: 'question_answer', route:'/about' }
            ],
            drawer: false

        }
    },
    methods:{
        signOut(){
            let url = types.BASE_DOMAIN+"/admin/logout";
            this.api.post(url).then(() =>{
                store.dispatch("logout");
                router.push({
                    path:"login"
                });
            }).catch(e => {
                console.log(e);
            });
        }
    }
  }
</script>

<style>

</style>
