<script lang="ts">
export default {
  data() {
    const localPosts = localStorage.getItem('postsVue')
    return {
      posts: localPosts
        ? (JSON.parse(localPosts) as { title: string; text: string; id: number }[])
        : [],
      user: localStorage.getItem('nameVue'),
      showAside: false,
      titleVue: '',
      postVue: '',
      number: localPosts ? JSON.parse(localPosts).length : 0,
      selectedPost: null as number | null,
      postSelected: false,
    }
  },
  methods: {
    logout() {
      localStorage.removeItem('nameVue')
      localStorage.removeItem('emailVue')
      this.user = null
      window.location.reload()
    },
    asideShow() {
      if (this.postSelected == true) {
        this.postSelected = false
      } else {
        this.showAside = !this.showAside
      }
    },
    storeText() {
      this.posts.push({
        title: this.titleVue,
        text: this.postVue,
        id: (this.number += 1),
      })

      localStorage.setItem('postsVue', JSON.stringify(this.posts))
    },
    showPost(id: number) {
      // console.log(id)
      this.selectedPost = id
      this.showAside = !this.showAside
      this.postSelected = true
    },
    deleteItem() {
      const post = this.posts.find((item) => {
        return item.id === this.selectedPost
      })
      this.posts = this.posts.filter((item) => {
        return item !== post
      })
      localStorage.setItem('postsVue', JSON.stringify(this.posts))
      window.location.reload()
    },
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
            <button type="button" class="button is-text" @click="logout">Logout</button>
          </div>
        </div>

        <div class="is-flex">
          <div class="is-flex-grow-1">
            <div class="block is-flex is-justify-content-space-between">
              <p class="title">Posts</p>
              <button type="button" class="button is-link" @click="asideShow">Add New Post</button>
            </div>

            <table
              class="table is-fullwidth is-striped is-hoverable is-narrow"
              v-if="posts.length > 0"
            >
              <thead>
                <tr class="has-background-link-light">
                  <th>ID</th>
                  <th>Title</th>
                  <th class="has-text-right">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="post in posts" :key="post.id">
                  <td>{{ post.id }}</td>
                  <td>{{ post.title }}</td>
                  <td class="has-text-right is-vcentered">
                    <button
                      type="button"
                      :class="
                        showAside && selectedPost === post.id ? 'button is-text' : 'button is-link'
                      "
                      @click="showPost(post.id)"
                    >
                      {{ showAside && selectedPost === post.id ? 'Close' : 'Open' }}
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
            <p class="has-text-centered" v-else>No posts yet.</p>
          </div>

          <div
            v-if="!postSelected"
            class="is-flex is-flex-direction-column"
            :style="{
              width: showAside ? '500px' : '0px',
              height: showAside ? 'auto' : '0px',
              minWidth: showAside ? '500px' : '0px',
              marginLeft: showAside ? '1.5rem' : '0px',
              paddingLeft: showAside ? '20px' : '0px',
              gap: showAside ? '15px' : '0px',
              overflow: 'hidden',
              transition: '0.4s',
            }"
          >
            <h2>Create new posts</h2>

            <form @submit="storeText">
              <div class="is-flex is-flex-direction-column" style="gap: 10px">
                <label for="titleVue">Title </label>
                <input
                  type="text"
                  name="titleVue"
                  id="titleVue"
                  placeholder="Post Title"
                  class="input"
                  v-model="titleVue"
                  required
                />
                <label for="postVue">Write Post Body</label>
                <textarea
                  class="input"
                  placeholder="Post body"
                  id="postVue"
                  name="postVue"
                  v-model="postVue"
                  required
                ></textarea>
              </div>
              <div class="field is-grouped mt-5">
                <div class="control">
                  <button type="submit" class="button is-link">Create</button>
                </div>
                <div class="control">
                  <button type="button" class="button is-link is-light" @click="asideShow">
                    Cancel
                  </button>
                </div>
              </div>
            </form>
          </div>
          <div
            v-else
            :style="{
              width: showAside ? '500px' : '0px',
              height: showAside ? 'auto' : '0px',
              minWidth: showAside ? '500px' : '0px',
              marginLeft: showAside ? '1.5rem' : '0px',
              paddingLeft: showAside ? '20px' : '0px',
              gap: showAside ? '15px' : '0px',
              overflow: 'hidden',
              transition: '0.4s',
            }"
          >
            <div class="is-flex is-justify-content-space-between is-align-items-center">
              <h2 class="title">
                #{{ posts.find((post) => post.id === selectedPost)?.id }}:
                {{ posts.find((post) => post.id === selectedPost)?.title }}
              </h2>
              <div>
                <button type="button" @click="deleteItem">
                  <i class="fa fa-trash has-text-danger"></i>
                </button>
              </div>
            </div>
            <p>{{ posts.find((post) => post.id === selectedPost)?.text }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
