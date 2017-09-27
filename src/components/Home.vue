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

    <div>
      <div class="layout-padding">
        <div v-if="!authenticated">
          <div class="column items-center">
            <div class="auto">
              <h3>
                </br>
                <router-link to="/signin">Sign In</router-link> ou
                <router-link to="/register">Registre-se</router-link>
                </br>
                </br>
              </h3>
            </div>
          </div>
        </div>
        <div v-else class="column items-center">
          <h5>
            To make this demo work correctly first register your avatar to
            <a href="https://www.gravatar.com">https://www.gravatar.com</a>
            then go to the
            <router-link to="/chat">Chat</router-link>
          </h5>
        </div>
      </div>
    </div>

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
  }
}
</script>