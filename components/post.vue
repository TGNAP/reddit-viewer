<template>
  <v-card>
    <v-card-title> {{ title }} </v-card-title>
    <v-card-subtitle>
      Posted by {{ author }}
      <span v-if="!!authorFlairText"> ({{ authorFlairText }})</span>
      ({{ createdDate() }})
    </v-card-subtitle>
    <v-chip v-if="!!flairText" class="mx-4 mb-4"> {{ flairText }} </v-chip>
    <v-card-text v-if="!!selftext" class="pt-0">
      {{ selftext }}
    </v-card-text>
    <v-img v-if="!!src" class="pt-0" :lazy-src="lazySrc" :src="src" />
    <div class="pa-4">
      <v-chip> {{ score }} upvotes </v-chip>
      <v-chip> {{ numComments }} comments </v-chip>
      <v-chip v-if="numCrossposts > 0"> {{ numCrossposts }} crossposts </v-chip>
    </div>
  </v-card>
</template>

<script>
export default {
  props: {
    title: {
      default: 'Error fetching the post title',
      type: String,
    },
    author: {
      default: 'unknown',
      type: String,
    },
    authorFlairText: {
      default: '',
      type: String,
    },
    created: {
      default: 0,
      type: Number,
    },
    selftext: {
      default: '',
      type: String,
    },
    lazySrc: {
      default: '',
      type: String,
    },
    src: {
      default: '',
      type: String,
    },
    flairText: {
      default: '',
      type: String,
    },
    score: {
      default: 0,
      type: Number,
    },
    numComments: {
      default: 0,
      type: Number,
    },
    numCrossposts: {
      default: 0,
      type: Number,
    },
  },
  methods: {
    createdDate() {
      return new Date(this.created * 1000).toUTCString()
    },
  },
}
</script>
