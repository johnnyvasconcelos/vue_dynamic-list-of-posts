<script lang="ts">
import FormPost from './FormPost.vue'
import Loader from './LoadingView.vue'
export default {
  components: {
    FormPost,
    Loader,
  },
  data() {
    const localPosts = localStorage.getItem('postsVue')
    const localComments = localStorage.getItem('commentsVue')
    return {
      posts: localPosts
        ? (JSON.parse(localPosts) as { title: string; text: string; id: number }[])
        : [],
      comments: localComments
        ? (JSON.parse(localComments) as {
            author: string
            email: string
            authorId: number | null
            postId: number
            message: string
          }[])
        : ([] as {
            author: string
            email: string
            authorId: number | null
            postId: number
            message: string
          }[]),
      user: localStorage.getItem('nameVue'),
      showAside: false,
      titleVue: '',
      postVue: '',
      number: localPosts ? JSON.parse(localPosts).length : 0,
      ID: localComments ? JSON.parse(localComments).length : 0,
      selectedPost: null as number | null,
      postSelected: false,
      isComment: false,
      authorName: '',
      authorEmail: '',
      postMessage: '',
      isLoading: false,
      isLoadingLoad: true,
      editForm: false,
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
      if (this.showAside && this.selectedPost === id) {
        this.showAside = false
        this.postSelected = false
      } else {
        this.selectedPost = id
        this.showAside = true
        this.postSelected = true
        this.isLoading = true
        setTimeout(() => {
          this.isLoading = false
        }, 2000)
      }
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
    editItem() {
      this.editForm = true
    },
    showCommentForm() {
      this.isComment = true
    },
    cancelComment() {
      this.isComment = false
      this.authorName = ''
      this.authorEmail = ''
      this.postMessage = ''
    },
    addComment() {
      this.comments.push({
        author: this.authorName,
        authorId: this.selectedPost,
        postId: (this.ID += 1),
        email: this.authorEmail,
        message: this.postMessage,
      })
      this.isComment = false
      localStorage.setItem('commentsVue', JSON.stringify(this.comments))
      this.postMessage = ''
    },
    deletePost(commentId: number) {
      this.comments = this.comments.filter((c) => {
        return c.postId !== commentId
      })
      localStorage.setItem('commentsVue', JSON.stringify(this.comments))
    },
  },
  created() {
    setTimeout(() => {
      this.isLoadingLoad = false
    }, 2000)
  },
}
</script>

<template>
  <div class="tile is-parent">
    <div class="tile is-child box is-success">
      <div class="block">
        <nav class="navbar" role="navigation" aria-label="main navigation">
          <div class="navbar-item">
            <h2 class="is-size-4">Vue List Of Posts</h2>
          </div>
          <div class="navbar-end">
            <div class="navbar-item">
              <div class="buttons">
                <div class="mr-5 mb-2">
                  <p>User: {{ user }}</p>
                </div>

                <a class="button is-light" @click="logout"> Logout </a>
              </div>
            </div>
          </div>
        </nav>

        <div class="tile is-parent">
          <div class="tile is-child box is-success">
            <div class="is-flex" style="align-items: flex-start; width: 100%">
              <div class="block" style="flex-grow: 1; min-width: 0">
                <div class="block is-flex is-justify-content-space-between">
                  <p class="title">Posts</p>
                  <button type="button" class="button is-link" @click="asideShow">
                    Add New Post
                  </button>
                </div>

                <Loader v-if="isLoadingLoad" />

                <div v-else>
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
                              showAside && selectedPost === post.id
                                ? 'button is-text'
                                : 'button is-link'
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
              </div>

              <div
                class="is-flex is-flex-direction-column"
                :class="{ 'Sidebar--open': showAside }"
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
                <div v-if="!postSelected" class="is-flex is-flex-direction-column">
                  <h2>Create new posts</h2>
                  <FormPost
                    :storeText="storeText"
                    :asideShow="asideShow"
                    :titleVue="titleVue"
                    :postVue="postVue"
                    @update:titleVue="titleVue = $event"
                    @update:postVue="postVue = $event"
                  />
                </div>

                <div v-else class="is-flex is-flex-direction-column">
                  <div class="is-flex is-justify-content-space-between is-align-items-center">
                    <h2 class="title">
                      #{{ posts.find((post) => post.id === selectedPost)?.id }}:
                      {{ posts.find((post) => post.id === selectedPost)?.title }}
                    </h2>
                    <div class="is-flex" style="gap: 10px">
                      <button @click="editItem">
                        <i class="fa fa-pencil has-text-link"></i>
                      </button>

                      <button type="button" @click="deleteItem">
                        <i class="fa fa-trash has-text-danger"></i>
                      </button>
                    </div>
                  </div>

                  <p>{{ posts.find((post) => post.id === selectedPost)?.text }}</p>

                  <div v-if="comments.length > 0"></div>
                  <div v-if="comments.length == 0">
                    <h2 class="mt-5 title">No comments yet</h2>
                  </div>

                  <Loader v-if="isLoading" />

                  <div v-else>
                    <div v-if="comments.length > 0 && !isComment" class="mt-5">
                      <article
                        class="is-small message mt-3"
                        v-for="comment in comments.filter((c) => c.authorId === selectedPost)"
                        :key="comment.postId"
                      >
                        <div class="is-flex is-justify-content-space-between">
                          <a :href="`mailto:${comment.email}`">{{ comment.author }}</a>
                          <button
                            type="button"
                            class="button is-small is-text has-text-danger"
                            @click="deletePost(comment.postId)"
                          >
                            <i class="fa fa-close"></i>
                          </button>
                        </div>
                        <div>{{ comment.message }}</div>
                      </article>
                    </div>
                  </div>

                  <!-- create post -->
                  <form v-if="isComment" class="mt-5">
                    <div class="is-flex is-flex-direction-column" style="gap: 10px">
                      <label for="authorName">Author Name</label>
                      <input
                        type="text"
                        class="input"
                        id="authorName"
                        v-model="authorName"
                        name="authorName"
                        placeholder="Name Surname"
                      />

                      <label for="authorEmail">Author Email</label>
                      <input
                        type="email"
                        placeholder="Your Email"
                        id="authorEmail"
                        class="input"
                        v-model="authorEmail"
                        required
                      />

                      <label for="postComment">Write Post Body</label>
                      <textarea
                        class="textarea"
                        name="postMessage"
                        id="postMessage"
                        placeholder="Comment"
                        v-model="postMessage"
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
                      <button class="button is-link" @click="addComment">Add comment</button>&nbsp;
                      <button class="button is-text" @click="cancelComment">Cancel</button>
                    </div>
                  </form>

                  <!-- edit post -->
                  <form v-if="editForm" class="mt-5">
                    <div class="is-flex is-flex-direction-column" style="gap: 10px">
                      <label for="authorName">Author Name</label>
                      <input
                        type="text"
                        class="input"
                        id="authorName"
                        v-model="authorName"
                        name="authorName"
                        placeholder="Name Surname"
                      />

                      <label for="authorEmail">Author Email</label>
                      <input
                        type="email"
                        placeholder="Your Email"
                        id="authorEmail"
                        class="input"
                        v-model="authorEmail"
                        required
                      />

                      <label for="postComment">Write Post Body</label>
                      <textarea
                        class="textarea"
                        name="postMessage"
                        id="postMessage"
                        placeholder="Comment"
                        v-model="postMessage"
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
                      <button class="button is-link" @click="addComment">Add comment</button>&nbsp;
                      <button class="button is-text" @click="cancelComment">Cancel</button>
                    </div>
                  </form>

                  <button v-if="!isComment" class="mt-5 button is-link" @click="showCommentForm">
                    Write a comment
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
