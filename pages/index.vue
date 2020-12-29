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
      'https://api.pushshift.io/reddit/submission/search/' +
        // Restrict to a specific subreddit
        // String or comma-delimited string (Multiple values allowed)
        '?subreddit=' +
        this.parameters.subreddit +
        // Get specific submissions via their ids
        // Comma-delimited base36 ids
        '&ids=' +
        this.parameters.ids +
        // Search term. Will search ALL possible fields
        // String / Quoted String for phrases
        '&q=' +
        this.parameters.query +
        // Searches the title field only
        // String / Quoted String for phrases
        '&title=' +
        this.parameters.title +
        // Searches the selftext (the content of the textposts) field only
        // String / Quoted String for phrases
        '&selftext=' +
        this.parameters.selftext +
        // Number of results to return
        // Integer <= 500
        '&size=' +
        this.parameters.size +
        // Sort results in a specific order
        // "asc", "desc"
        '&sort=' +
        this.parameters.sort +
        // Sort by a specific attribute
        // "score", "num_comments", "created_utc"
        '&sort_type=' +
        this.parameters.sort_type +
        // Restrict to a specific author
        // String or comma-delimited string (Multiple values allowed)
        '&author=' +
        this.parameters.author +
        // Return results after this date
        // Epoch value or Integer + "s,m,h,d" (i.e. 30d for 30 days)
        '&after=' +
        this.parameters.after +
        // Return results before this date
        // Epoch value or Integer + "s,m,h,d" (i.e. 30d for 30 days)
        '&before=' +
        this.parameters.before +
        // Restrict results based on score
        // Integer or > x or < x (i.e. score=>100 or score=<25)
        '&score=' +
        this.parameters.score +
        // Restrict results based on number of comments
        // Integer or > x or < x (i.e. num_comments=>100)
        '&num_comments=' +
        this.parameters.num_comments +
        // Restrict to nsfw or sfw content
        // "true" or "false"
        '&over_18=' +
        this.parameters.over_18 +
        // Restrict to video content
        // "true" or "false"
        '&is_video=' +
        this.parameters.is_video +
        // Return locked or unlocked threads only
        // "true" or "false"
        '&locked=' +
        this.parameters.locked +
        // Return stickied or unstickied content only
        // "true" or "false"
        '&stickied=' +
        this.parameters.stickied +
        // Exclude or include spoilers only
        // "true" or "false"
        '&spoiler=' +
        this.parameters.spoiler +
        // Exclude or include content mode submissions
        // "true" or "false"
        '&contest_mode=' +
        this.parameters.contest_mode +
        // display metadata about the query
        // ["true", "false"]
        '&metadata' +
        this.parameters.metadata
      // TODO: q:not, title:not, selftext:not, fields, aggs (https://github.com/pushshift/api#using-the-aggs-parameter), frequencys
    )
  },
  data: () => ({
    show: false,
    posts: {},
    parameters: {
      subreddit: 'teengamingnights',
      ids: '',
      query: '',
      title: '',
      selftext: '',
      size: 25,
      fields: '',
      sort: 'desc',
      sort_type: 'created_utc',
      aggs: '',
      author: '',
      after: '',
      before: '',
      score: '',
      num_comments: '',
      over_18: '',
      is_video: '',
      locked: '',
      stickied: '',
      spoiler: '',
      contest_mode: '',
      frequency: '',
      metadata: '',
    },
  }),
  methods: {
    sendMessage() {
      this.$fetch()
    },
    clearQuery() {
      this.message = ''
    },
  },
}
</script>
