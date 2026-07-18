<script lang="ts">
import FormPost from './FormPost.vue'
import Loader from './LoadingView.vue'
import FormComment from './FormComment.vue'
import FormEdit from './FormEdit.vue'
export default {
  components: {
    FormPost,
    FormComment,
    FormEdit,
    Loader,
  },
  data() {
    const localPosts = localStorage.getItem('postsVue')
    const localComments = localStorage.getItem('commentsVue')
    return {
      posts: [],
      comments: [],
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
      this.editForm = false
    },
    storeText() {
      const newPost = {
        title: this.titleVue,
        body: this.postVue,
        userId: 3123,
      }
      fetch('https://mate.academy/students-api/posts', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
        },
        body: JSON.stringify(newPost),
      })
        .then((response) => response.json())
        .then((post) => {
          this.posts.push(post)
          this.asideShow()
        })
        .catch((error) => console.error('Erro:', error))
    },
    showPost(id: number) {
      if (this.showAside && this.selectedPost === id) {
        this.showAside = false
        this.postSelected = false
        this.editForm = false
      } else {
        this.selectedPost = id
        this.showAside = true
        this.postSelected = true
        this.editForm = false
        this.isLoading = true
        fetch('https://mate.academy/students-api/comments')
          .then((response) => {
            if (!response.ok) throw new Error('Erro')
            return response.json()
          })
          .then((allComments) => {
            this.comments = allComments.filter((c) => c.postId === id)
          })
          .catch((error) => {
            console.error('Erro:', error)
          })
          .finally(() => {
            this.isLoading = false
          })
      }
      this.editForm = false
    },
    deleteItem() {
      const post = this.posts.find((item) => {
        return item.id === this.selectedPost
      })
      this.posts = this.posts.filter((item) => {
        return item !== post
      })
      fetch(`https://mate.academy/students-api/posts/${this.selectedPost}`, {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
        },
        body: JSON.stringify(post),
      })
        .then((response) => response.json())
        .then(() => {
          this.isLoadingLoad = true
          setTimeout(() => {
            this.isLoadingLoad = false
          }, 2000)
          this.showAside = false
        })
        .catch((error) => {
          console.error('Erro:', error)
        })
    },
    editItem() {
      const post = this.posts.find((p) => p.id === this.selectedPost)
      if (post) {
        this.titleVue = post.title
        this.postVue = post.body
      }
      this.editForm = true
      this.isComment = false
    },
    addEditForm() {
      const newPost = {
        title: this.titleVue,
        body: this.postVue,
        userId: 1,
      }
      fetch(`https://mate.academy/students-api/posts/${this.selectedPost}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
        },
        body: JSON.stringify(newPost),
      })
        .then((response) => response.json())
        .then((post) => {
          const postToEdit = this.posts.find((p) => p.id === this.selectedPost)
          postToEdit.title = post.title
          postToEdit.body = post.body
          this.editForm = false
        })
        .catch((error) => console.error('Erro:', error))
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
    cancelEditForm() {
      this.editForm = false
    },
    addComment() {
      this.isLoading = true
      const newComment = {
        postId: this.selectedPost,
        body: this.postMessage,
        name: this.authorName,
        email: this.authorEmail,
      }
      //console.log(newComment)
      fetch('https://mate.academy/students-api/comments', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
        },
        body: JSON.stringify(newComment),
      })
        .then((response) => response.json())
        .then((comment) => {
          this.comments.push(comment)
          this.isComment = false
          this.editForm = false
          this.postMessage = ''
          this.isLoading = false
        })
        .catch((error) => console.error('Erro:', error))
    },
    deletePost(commentId: number) {
      const comment = this.comments.find((item) => item.id === commentId)
      this.comments = this.comments.filter((item) => item.id !== commentId)

      fetch(`https://mate.academy/students-api/comments/${commentId}`, {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
        },
        body: JSON.stringify(comment),
      })
        .then((response) => response.json())
        .then(() => {
          this.isLoadingLoad = true
          setTimeout(() => {
            this.isLoadingLoad = false
          }, 2000)
          this.showAside = false
        })
        .catch((error) => {
          console.error('Erro:', error)
        })
    },
  },
  created() {
    /*
    setTimeout(() => {
      this.isLoadingLoad = false
    }, 2000)
 */
    this.isLoadingLoad = true
    fetch('https://mate.academy/students-api/posts')
      .then((response) => {
        if (!response.ok) {
          throw new Error('Erro')
        }
        return response.json()
      })
      .then((dados) => {
        this.posts = dados
      })
      .catch((error) => {
        console.error(error)
      })
      .finally(() => {
        this.isLoadingLoad = false
      })

    fetch('https://mate.academy/students-api/comments')
      .then((response) => {
        if (!response.ok) {
          throw new Error('Erro')
        }
        return response.json()
      })
      .then((dados) => {
        this.comments = dados
      })
      .catch((error) => {
        console.error(error)
      })
      .finally(() => {
        this.isLoadingLoad = false
      })
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
                <div v-if="!postSelected && !editForm" class="is-flex is-flex-direction-column">
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

                  <p>{{ posts.find((post) => post.id === selectedPost)?.body }}</p>

                  <div v-if="comments.length > 0"></div>
                  <div v-if="comments.length == 0">
                    <h2 class="mt-5 title">No comments yet</h2>
                  </div>

                  <Loader v-if="isLoading" />

                  <div v-else>
                    <div v-if="comments.length > 0 && !isComment && !editForm" class="mt-5">
                      <article
                        class="is-small message mt-3"
                        v-for="comment in comments.filter((c) => c.postId === selectedPost)"
                        :key="comment.id"
                      >
                        <div class="is-flex is-justify-content-space-between">
                          <a :href="`mailto:${comment.email}`">{{ comment.email }}</a>
                          <button
                            type="button"
                            class="button is-small is-text has-text-danger"
                            @click="deletePost(comment.id)"
                          >
                            <i class="fa fa-close"></i>
                          </button>
                        </div>
                        <div>{{ comment.body }}</div>
                      </article>
                    </div>
                  </div>

                  <!-- add a comment -->
                  <FormComment
                    v-if="isComment"
                    :isComment="isComment"
                    @update:authorName="authorName = $event"
                    @update:authorEmail="authorEmail = $event"
                    @update:postMessage="postMessage = $event"
                    :addComment="addComment"
                    :cancelComment="cancelComment"
                  />

                  <!-- edit post -->
                  <FormEdit
                    v-if="editForm"
                    :titleVue="titleVue"
                    :postVue="postVue"
                    @update:titleVue="titleVue = $event"
                    @update:postVue="postVue = $event"
                    :cancelEditForm="cancelEditForm"
                    :addEditForm="addEditForm"
                  />

                  <button
                    v-if="!isComment && !editForm"
                    class="mt-5 button is-link"
                    @click="showCommentForm"
                  >
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
