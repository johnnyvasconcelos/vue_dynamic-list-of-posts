<script lang="ts">
import Loader from './LoadingView.vue'
export default {
  components: {
    Loader,
  },
  data() {
    return {
      email: '',
      name: '',
      first: true,
      logged: false,
      isLoading: false,
    }
  },
  methods: {
    handleSubmit() {
      if (this.first) {
        if (!this.verification()) {
          this.first = false
        } else {
          this.isLoading = true
          this.logged = true
          localStorage.setItem('emailVue', this.email)
        }
      } else {
        this.isLoading = true
        this.logged = true
        localStorage.setItem('emailVue', this.email)
        localStorage.setItem('nameVue', this.name)
        localStorage.setItem('loggedVue', 'yes')

        setTimeout(() => {
          window.location.reload()
        }, 2000)
      }
    },
    verification() {
      return localStorage.getItem('nameVue') && localStorage.getItem('loggedVue')
    },
  },
}
</script>

<template>
  <section class="container is-flex is-justify-content-center">
    <form @submit.prevent="handleSubmit" class="box mt-5" v-if="!isLoading">
      <h1 class="title is-3">You need to register</h1>

      <div class="field">
        <label class="label" for="user-email"> Email </label>

        <div class="control has-icons-left">
          <input
            type="email"
            id="user-email"
            name="email"
            class="input"
            placeholder="Enter your email"
            v-model="email"
            required
          />

          <span class="icon is-small is-left">
            <i class="fas fa-envelope" />
          </span>
        </div>

        <p class="help is-danger">error message</p>
      </div>

      <div class="field" v-if="!first">
        <label class="label" for="user-name"> Your Name </label>

        <div class="control has-icons-left">
          <input
            type="text"
            id="user-name"
            name="name"
            class="input"
            placeholder="Enter your name"
            required
            minlength="4"
            v-model="name"
          />

          <span class="icon is-small is-left">
            <i class="fas fa-user" />
          </span>
        </div>

        <p class="help is-danger">error message</p>
      </div>

      <div class="field">
        <button type="submit" class="button is-primary">Login</button>
      </div>
    </form>
    <Loader v-else />
  </section>
</template>
