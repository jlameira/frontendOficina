<template>
  <q-layout ref="layout">
    <q-toolbar slot="header">
      <q-toolbar-title>
        Quasar Saas
      </q-toolbar-title>
      <q-btn flat @click="goTo('signin')" v-show="!authenticated">
        Sign In
      </q-btn>
      <q-btn flat @click="goTo('register')" v-show="!authenticated">
        Registrar
      </q-btn>
    </q-toolbar>
    <div slot="left" ref="menu" v-if="authenticated">
    </div>

    <!-- sub-routes -->
    <router-view :user="user"></router-view>

  </q-layout>
</template>

<script>
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
    debugger
    this.$router.push({ name: 'profile' })
    this.$data.user = localStorage.getItem('user')
  },
  methods: {
    goTo(route) {
      this.$router.push({ name: route })
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
    }
  },
  beforeDestroy() {
  }
}
</script>