<script lang="ts">
import type { PropType } from 'vue'
export default {
  props: {
    isComment: {
      type: Boolean,
    },
    authorName: {
      type: String,
    },
    authorEmail: {
      type: String,
    },
    postMessage: {
      type: String,
    },
    addComment: {
      type: Function as PropType<() => void>,
    },
    cancelComment: {
      type: Function as PropType<() => void>,
    },
  },
}
</script>
<template>
  <form class="mt-5">
    <div class="is-flex is-flex-direction-column" style="gap: 10px">
      <label for="authorName">Author Name</label>
      <input
        type="text"
        class="input"
        id="authorName"
        :value="authorName"
        name="authorName"
        placeholder="Name Surname"
      />

      <label for="authorEmail">Author Email</label>
      <input
        type="email"
        placeholder="Your Email"
        id="authorEmail"
        class="input"
        :value="authorEmail"
        required
      />

      <label for="postComment">Write Post Body</label>
      <textarea
        class="textarea"
        name="postMessage"
        id="postMessage"
        placeholder="Comment"
        :value="postMessage"
        required
      ></textarea>
    </div>

    <div class="is-hidden">
      <span className="icon is-small is-right has-text-danger" data-cy="ErrorIcon">
        <i className="fas fa-exclamation-triangle"></i>
      </span>
      <p className="help is-danger" data-cy="ErrorMessage">error text</p>
    </div>

    <div class="mt-5">
      <button class="button is-link" @click="addComment">Add comment</button>
      <button class="button is-text" @click="cancelComment">Cancel</button>
    </div>
  </form>
</template>
