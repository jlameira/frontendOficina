<template>
  <q-layout ref="layout">
    <q-toolbar slot="header">
      <q-btn flat @click="close">
        <q-icon name="keyboard_arrow_left" />
      </q-btn>
      <div class="q-toolbar-title">
        Header
      </div>
    </q-toolbar>

    <div class="flex justify-center full-height">
      <q-card>
        <q-card-title>Registre-se</q-card-title>
        <q-card-main>
          <form>
            <q-field>
              <q-input v-model="form.email" float-label="Email" @blur="$v.form.email.$touch" @keyup.enter="submit" :error="$v.form.email.$error" />
            </q-field>

            <q-field>
              <q-input v-model="form.password" type="password" float-label="Password" @blur="$v.form.password.$touch" @keyup.enter="submit" :error="$v.form.password.$error" />
              <span class="form-group__message" v-if="!$v.form.password.minLength">A senha deve ter pelo menos {{ $v.form.password.$params.minLength.min }} letras.</span>
            </q-field>
            <q-field>
              <q-input v-model="form.confirm_password" type="password" float-label="Confirme Password" />
            </q-field>
          </form>
        </q-card-main>

        <q-card-separator/>

        <q-card-actions>
          <q-btn @click="login" type="submit">Login</q-btn>
        </q-card-actions>
      </q-card>
    </div>
  </q-layout>
</template>

<script>
const BASE_URL = 'http://localhost:3003/open'
import axios from 'axios'

import {
  Toast, dom, event, openURL, QLayout, QToolbar, QToolbarTitle, QBtn, QIcon, QList, QListHeader, QItem, QItemSide, QItemMain
} from 'quasar'
import { required, email, sameAs, minLength } from 'vuelidate/lib/validators'

export default {
  name: 'index',
  components: {
    QLayout, QToolbar, QToolbarTitle, QBtn, QIcon, QList, QListHeader, QItem, QItemSide, QItemMain
  },
  data() {
    return {
      form: {
        email: '',
        password: '',
        confirm_password: ''

      },
    }
  },
  validations: {
    form: {
      email: { required, email },
      password: {
        required,
        minLength: minLength(6)
      }
    }
  },
  computed: {

  },
  methods: {
    close() {
      this.$router.push({ name: 'index' })
    },
    launch(url) {
      openURL(url)
    },
    login(evt) {
      const passwordRegex = /((?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[@#$%]).{6,20})/
      this.$v.form.$touch()
      if (this.$v.form.email.$error) {
        Toast.create('Informe um email válido.')
        return
      }
      if (this.$v.form.password.$error || !this.form.password.match(passwordRegex)) {
        Toast.create('A senha deve conter letras Maiúscula, minúscula,número ou algum caracter especial .')
        return
      }
      axios.post(`${BASE_URL}/signup`, { email: this.form.email, password: this.form.password, confirm_password: this.form.confirm_password })
        .then(function(response) {
          console.log(response);
        })
        .catch(function(error) {
          Toast.create.negative('não foi possível realizar o cadastro')
          console.log(error);
        });
      console.log(this.form.password + ' - ', this.form.email)
    }
  }
}
</script>
