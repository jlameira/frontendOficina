<template>

</template>

<script>
import { Toast, Dialog } from 'quasar'
const BASE_URL = 'http://localhost:3003/open'
import axios from 'axios'
export default {
  data() {
    return {
    }
  },
  computed: {
  },
  methods: {
    isRegistration() {
      return this.$route.name === 'register'
    },
    register(email, password) {
      // return api.service('users').create({
      //   email: email,
      //   password: password
      // })
    },
    login(email, password) {
      return axios.post(`${BASE_URL}/login`, { email: email, password: password });
    }
  },
  mounted() {
    Dialog.create({
      title: this.isRegistration() ? 'Register' : 'Sign In',
      form: {
        email: {
          type: 'email',
          label: 'E-mail',
          model: ''
        },
        password: {
          type: 'password',
          label: 'Password',
          model: ''
        }
      },
      onDismiss: () => {
        this.$router.push({ name: 'home' })
      },
      buttons: [
        {
          label: 'Ok',
          handler: (data) => {
            if (this.isRegistration()) {
              this.register(data.email, data.password)
                .then(() => {
                  return this.login(data.email, data.password)
                })
                .then(_ => {
                  Toast.create.positive('You are now logged in')
                })
                .catch(_ => {
                  Toast.create.negative('Cannot register, please check your e-mail or password')
                  this.$router.push({ name: 'home' })
                })
            }
            else {
              let app = this;
              this.login(data.email, data.password)
                .then((response) => {
                  Toast.create.positive('Agora você está logado');
                  localStorage.setItem('user', response.data.name);
                  localStorage.setItem('token', response.data.token);
                  console.log(response);
                })
                .then(resp => {
                  debugger
                  this.$router.push({ name: 'home' })
                })
                .catch(_ => {
                  Toast.create.negative('não foi possível realizar login, por favor verifique seu email ou password')
                  this.$router.push({ name: 'home' })
                });
            }
          }
        }
      ]
    })
  },
  beforeDestroy() {
  }
}
</script>

<style lang="styl">

</style>