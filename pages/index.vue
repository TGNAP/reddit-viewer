<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="3">
      <v-card>
        <v-card-title class="headline justify-center"> Filtering </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="query"
            filled
            clearable
            label="Query"
            type="text"
            @click:clear="clearQuery"
            @keyup.enter="sendMessage()"
          ></v-text-field>
        </v-card-text>
        <v-card-actions class="justify-center">
          <v-btn ripple @click.once="sendMessage()"> Apply </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
    <v-col v-if="$fetchState.pending" cols="12" sm="8" md="9">
      Fetching 25 posts from Pushshift...
    </v-col>
    <v-col v-else-if="$fetchState.error" cols="12" sm="8" md="9">
      An error has occurred :( Try reloading the page, or contacting me via
      Discord: Wuzado#3715
    </v-col>
    <v-col v-else cols="12" sm="8" md="9">
      <h1>Posts:</h1>
      <div v-for="post in posts.data" :key="post.id">
        <post
          class="my-2"
          :title="post.title"
          :author="post.author"
          :created="post.created_utc"
          :selftext="post.selftext"
          :lazy-src="post.thumbnail"
          :src="post.url"
          :flair-text="post.link_flair_text"
        />
      </div>
    </v-col>
  </v-row>
</template>

<script>
export default {
  async fetch() {
    this.posts = await this.$http.$get(
      'https://api.pushshift.io/reddit/submission/search/?subreddit=teengamingnights'
    )
  },
  data: () => ({
    show: false,
    query: '',
    posts: {},
  }),
  methods: {
    sendMessage() {
      this.clearQuery()
    },
    clearQuery() {
      this.message = ''
    },
  },
}
</script>
