<script lang="ts">
import LoginView from './components/LoginView.vue'
import PostsView from './components/PostsView.vue'
export default {
  components: {
    LoginView,
    PostsView,
  },
  data() {
    return {
      emailVue: localStorage.getItem('emailVue') || null,
    }
  },
  methods: {
    login(email: string) {
      this.emailVue = email
    },
  },
}
</script>
<template>
  <div id="app">
    <LoginView v-if="!emailVue" @onLogin="login" />
    <PostsView v-else :email="emailVue" />
  </div>
</template>
