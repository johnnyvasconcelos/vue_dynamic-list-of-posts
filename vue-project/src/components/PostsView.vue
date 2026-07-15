<script lang="ts">
export default {
  data() {
    return {
      posts: false,
      user: localStorage.getItem('nameVue'),
    }
  },
}
</script>

<template>
  <div class="tile is-parent">
    <div class="tile is-child box is-success">
      <div class="block">
        <div class="block is-flex is-justify-content-space-between">
          <p class="subtitle">Vue List of Posts</p>
          <div class="is-flex is-align-items-center is-justify-content-space-between">
            <p>User: {{ user }}</p>
            &nbsp;&nbsp;
            <button type="button" class="button is-text">Logout</button>
          </div>
        </div>

        <div class="block is-flex is-justify-content-space-between">
          <p class="title">Posts</p>
          <button type="button" class="button is-link">Add New Post</button>
        </div>

        <table class="table is-fullwidth is-striped is-hoverable is-narrow" v-if="posts">
          <thead>
            <tr class="has-background-link-light">
              <th>ID</th>
              <th>Title</th>
              <th class="has-text-right">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>id</td>
              <td>title</td>
              <td class="has-text-right is-vcentered">
                <button type="button" class="button is-link">Open</button>
              </td>
            </tr>
          </tbody>
        </table>
        <p class="has-text-centered" v-else>No posts yet.</p>
      </div>
    </div>
  </div>
</template>
