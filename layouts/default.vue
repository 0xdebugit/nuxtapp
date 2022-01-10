<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      temporary
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    
    <v-app-bar
      :clipped-left="clipped"
      app
    >
      <v-app-bar-nav-icon @click="drawer = !drawer" />
      <!-- <v-btn
        icon
        @click.stop="miniVariant = !miniVariant"
      >
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="clipped = !clipped"
      >
        <v-icon>mdi-application</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="fixed = !fixed"
      >
        <v-icon>mdi-minus</v-icon>
      </v-btn> -->
      <Glare />    
      <v-toolbar-title v-text="title" />
      <v-spacer />
        <!-- <v-switch 
          :value="darkMode" 
          @change="toggleDarkMode" 
          inset
          v-icon="'mdi-magnify'"    
        ></v-switch> -->

          <v-icon
          @click="toggleDarkMode"
          >
            {{darkModeIcon}}
          </v-icon>


    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer
      :absolute="!fixed"
      app
    >
      <v-card-text class="py-2 text-center">
        {{ new Date().getFullYear() }} — Made with 
            <v-icon
              color="red darken-2"
            >
              mdi-heart-flash
            </v-icon> by Bored Developer
      </v-card-text>

    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      darkMode: false,
      darkModeIcon: 'mdi-white-balance-sunny',
      clipped: false,
      drawer: false,
      rawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-lightbulb-on-outline',
          title: 'Bescom',
          to: '/'
        },
        {
          icon: 'mdi-information-outline',
          title: 'About',
          to: '/about'
        }
      ],
      title: 'Glare'
    }
  },
    methods: {
      toggleDarkMode: function () {
        this.$vuetify.theme.dark = !this.$vuetify.theme.dark;
        this.darkMode = !this.darkMode;
        if(!this.darkMode){
          this.darkModeIcon = 'mdi-white-balance-sunny';
        }else{
          this.darkModeIcon = 'mdi-weather-night';
        }
      }
    },
    computed: {
      switchLabel: function () {
        return this.darkMode ? 'light' : 'dark';
      }
    }  
}
</script>

<style>
      @font-face {
        font-family: "Archia";
        src: url("~@/assets/fonts/Archia/archia-bold-webfont.woff2") format("woff2"),
        url("~@/assets/fonts/Archia/archia-medium-webfont.woff2") format("woff2"),
        url("~@/assets/fonts/Archia/archia-semibold-webfont.woff2") format("woff2");
      }
</style>