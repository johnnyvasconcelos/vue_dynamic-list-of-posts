<template>
  <form @submit="storeText">
    <div class="is-flex is-flex-direction-column" style="gap: 10px">
      <label for="titleVue">Title</label>

      <input
        type="text"
        id="titleVue"
        class="input"
        placeholder="Post Title"
        :value="titleVue"
        @input="onTitleInput"
        required
      />

      <label for="postVue">Write Post Body</label>

      <textarea
        id="postVue"
        class="textarea"
        placeholder="Post body"
        :value="postVue"
        @input="onPostInput"
        required
      ></textarea>
    </div>

    <div class="field is-grouped mt-5">
      <div class="control">
        <button type="submit" class="button is-link">Create</button>
      </div>

      <div class="control">
        <button type="button" class="button is-link is-light" @click="asideShow">Cancel</button>
      </div>
    </div>
  </form>
</template>

<script lang="ts">
import { defineComponent, type PropType } from 'vue'

export default defineComponent({
  emits: ['update:titleVue', 'update:postVue'],

  props: {
    storeText: { type: Function as PropType<(payload: Event) => void>, required: true },
    asideShow: { type: Function as PropType<(payload: MouseEvent) => void>, required: true },
    titleVue: { type: String, required: true },
    postVue: { type: String, required: true },
  },

  methods: {
    onTitleInput(e: Event) {
      this.$emit('update:titleVue', (e.target as HTMLInputElement).value)
    },
    onPostInput(e: Event) {
      this.$emit('update:postVue', (e.target as HTMLTextAreaElement).value)
    },
  },
})
</script>
