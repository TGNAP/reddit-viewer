<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="3">
      <v-form v-model="valid">
        <v-card>
          <v-card-title class="headline justify-center">
            Filtering
          </v-card-title>
          <v-card-text>
            <v-text-field
              v-model="parameters.subreddit"
              clearable
              label="Subreddit"
              type="text"
              @click:clear="parameters.subreddit = ''"
              @keyup.enter="sendMessage()"
            />
            <v-text-field
              v-model="parameters.query"
              clearable
              label="Query"
              type="text"
              @click:clear="parameters.query = ''"
              @keyup.enter="sendMessage()"
            />
            <v-text-field
              v-model="parameters.title"
              clearable
              label="Title"
              type="text"
              @click:clear="parameters.title = ''"
              @keyup.enter="sendMessage()"
            />
            <v-text-field
              v-model="parameters.selftext"
              clearable
              label="Selftext"
              type="text"
              @click:clear="parameters.selftext = ''"
              @keyup.enter="sendMessage()"
            />
            <v-text-field
              v-model="parameters.size"
              :rules="sizeRules"
              clearable
              label="Number of results"
              type="text"
              @click:clear="parameters.size = ''"
              @keyup.enter="sendMessage()"
            />
            <v-radio-group v-model="parameters.order" label="Sorting order">
              <v-radio label="Ascending order" value="asc" />
              <v-radio label="Descending order" value="desc" />
            </v-radio-group>
            <v-radio-group v-model="parameters.sort" label="Sort by:">
              <v-radio label="Upvotes" value="score" />
              <v-radio label="ID" value="id" />
              <v-radio label="Creation time" value="created_utc" />
            </v-radio-group>
            <v-text-field
              v-model="parameters.author"
              clearable
              label="Author"
              type="text"
              @click:clear="parameters.author = ''"
              @keyup.enter="sendMessage()"
            />
            <!-- TODO: Dates (after, before), score, number of comments -->
          </v-card-text>
          <v-card-actions class="justify-center">
            <v-btn ripple :disabled="!valid" @click="sendMessage()">
              Apply
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-form>
    </v-col>
    <v-col v-if="$fetchState.pending" cols="12" sm="8" md="9">
      Fetching {{ parameters.size }} posts from Pushshift...
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
          :author-flair-text="post.author_flair_text"
          :created="post.created_utc"
          :selftext="post.selftext"
          :lazy-src="post.thumbnail"
          :src="post.url"
          :flair-text="post.link_flair_text"
          :score="post.score"
          :num-comments="post.num_comments"
          :num-crossposts="post.num_crossposts"
        />
      </div>
    </v-col>
  </v-row>
</template>

<script>
export default {
  async fetch() {
    this.posts = await this.$http.$get(
      'https://api.pushshift.io/reddit/submission/search' +
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
        '&order=' +
        this.parameters.order +
        // Sort by a specific attribute
        // "score", "id", "created_utc"
        '&sort=' +
        this.parameters.sort +
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
    valid: false,
    sizeRules: [
      (v) => !!v || 'Required.',
      (v) => /^\d+$/.test(v) || 'Must be a number!',
      (v) => v <= 500 || 'Number must be smaller than or equal 500!',
    ],
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
      order: 'desc',
      sort: 'created_utc',
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
  },
}
</script>
