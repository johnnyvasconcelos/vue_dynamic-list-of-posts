<script lang="ts">
import type { PropType } from 'vue'
export default {
  props: {
    titleVue: {
      type: String,
    },
    postVue: {
      type: String,
    },
    addEditForm: {
      type: Function as PropType<() => void>,
    },
    cancelEditForm: {
      type: Function as PropType<() => void>,
    },
  },
}
</script>

<template>
  <form class="mt-5" @submit.prevent>
    <div class="is-flex is-flex-direction-column" style="gap: 10px">
      <label for="editTitle">Post Title</label>
      <input
        type="text"
        class="input"
        id="editTitle"
        :value="titleVue"
        name="titleVue"
        placeholder="Title"
      />

      <label for="editContent">Write Post Body</label>
      <textarea
        class="textarea"
        name="postVue"
        id="editContent"
        placeholder="Content"
        :value="postVue"
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
      <button class="button is-link" @click="addEditForm" type="button">Save</button>&nbsp;
      <button class="button is-text" @click="cancelEditForm" type="button">Cancel</button>
    </div>
  </form>
</template>
