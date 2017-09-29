<template>
  <q-layout ref="layout">
    <q-toolbar slot="header">
      <q-toolbar-title>
        Quasar Authentication
      </q-toolbar-title>
      <q-btn flat @click="goTo('signin')" v-show="!authenticated">
        Sign In
      </q-btn>
      <q-btn flat @click="goTo('register')" v-show="!authenticated">
        Registrar
      </q-btn>
      <q-btn flat round @click="goTo('chat')" v-show="authenticated">
        <q-icon name="chat" />
        <q-tooltip anchor="bottom middle" self="top middle" :offset="[0, 20]">Chat</q-tooltip>
      </q-btn>
      <q-btn flat round @click="signout" v-show="authenticated">
        <q-icon name="exit_to_app" />
        <q-tooltip anchor="bottom middle" self="top middle" :offset="[0, 20]">Sair</q-tooltip>
      </q-btn>
    </q-toolbar>
    <div slot="left" ref="menu" v-if="authenticated">
      <q-side-link item to="/home">
        <q-item-side icon="home" />
        <q-item-main label="Home" />
      </q-side-link>
      <q-side-link item to="/chat">
        <q-item-side icon="chat" />
        <q-item-main label="Chat" />
      </q-side-link>
      <q-collapsible icon="info" label="About">
        <p style="padding: 25px;" class="text-grey-7">
          This is a template project combining the power of Quasar and Feathers to create real-time web apps.
        </p>
      </q-collapsible>
    </div>

    <!-- sub-routes -->
    <router-view :user="user"></router-view>

  </q-layout>
</template>

<script>
import axios from 'axios'
import consts from '../consts'
import {
  QLayout,
  QToolbar,
  QToolbarTitle,
  QBtn,
} from 'quasar'


export default {
  name: 'index',
  components: {
    QLayout,
    QToolbar,
    QToolbarTitle,
    QBtn
  },
  data() {
    return {
      user: null
    }
  },
  computed: {
    authenticated() {
      return this.$data.user !== null


    }
  },
  mounted() {
    // if (localStorage.getItem('token') !== null) {
    axios.post(`${consts.OAPI_URL}/validateToken`, { token: localStorage.getItem('token') || '' })
      .then(user => this.auth(user.data.valid))

    // } else {
    //   this.goTo('home')
    // }

  },
  methods: {
    goTo(route) {
      this.$router.push({ name: route })
    },
    auth(ok) {
      debugger
      if (ok) {
        this.$data.user = localStorage.getItem('user')
        return this.goTo('home');
      } else {
        localStorage.removeItem('user')
        localStorage.removeItem('token')
        this.$data.user = null
        return this.goTo('index');
      }

    },
    rightClickHandler: function(e) {
      e.preventDefault();
      if (e.target.tagName == 'SPAN') {
        this.itemName = e.target.innerHTML;
        this.enableContextMenu = true;
      }
      else {
        this.enableContextMenu = false;
      }
    },
    signout() {
      this.$data.user = null
      localStorage.removeItem('user');
      localStorage.removeItem('token');
      return this.goTo('home');
    }
  },
  beforeDestroy() {
  }
}
</script>
<style lang="styl">

</style>
