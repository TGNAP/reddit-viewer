<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline justify-center">
          Search the archives...
        </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="message"
            filled
            clearable
            label="Query"
            type="text"
            @click:clear="clearMessage"
            @keyup.enter="sendMessage()"
          ></v-text-field>
        </v-card-text>
        <v-card-actions class="justify-center">
          <v-btn ripple @click.once="sendMessage()"> Search </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data: () => ({
    show: false,
    message: '',
  }),
  methods: {
    sendMessage() {
      this.clearMessage()
    },
    clearMessage() {
      this.message = ''
    },
  },
}
</script>
