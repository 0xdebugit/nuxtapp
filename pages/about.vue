<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="12" md="10">
      <v-card>
        <v-card-title class="headline">
          Welcome to the <Glare /> Glare 
        </v-card-title>
        <v-card-text>
          <p>Hey Everyone, I have created this platform to mainly digitize & beautify the old-fashioned government notice/circular.</p>

          <p>I'm also focusing in building quick utility apps for people in Bengaluru.</p>

          <p>If you want to hangout, join telegram and if you like the work tag us on twitter.</p>

        </v-card-text>
        <v-card-actions>
          <v-spacer />

        <v-btn
          color="blue-grey"
          class="ma-2 white--text"
          href="https://t.me/glare_app"
          target="_blank"
        >
          Telegram
          <v-icon
            right
            dark
          >
            mdi-send
          </v-icon>
        </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'IndexPage'
}
</script>
