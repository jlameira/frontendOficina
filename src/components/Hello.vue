<template>
  <q-layout ref="layout" view="lHh Lpr fff" :left-class="{'bg-grey-2': true}">
    <q-toolbar slot="header" class="glossy">
      <!-- <q-btn flat @click="$refs.layout.toggleLeft()">
                <q-icon name="menu" />
              </q-btn> -->

      <q-toolbar-title>
        Quasar App
        <div slot="subtitle">Sistema </div>
      </q-toolbar-title>
    </q-toolbar>

    <!-- <div slot="left">
            </div> -->
    <div class="flex justify-center full-height" :class="heightSize">
      <form action="">
        <q-card>
          <q-card-title>Login</q-card-title>
          <q-card-main>
            <form>
              <q-field>
                <q-input v-model="form.email" float-label="Email" @blur="$v.form.email.$touch" @keyup.enter="submit" :error="$v.form.email.$error" />
              </q-field>

              <q-field>
                <q-input v-model="form.password" type="password" float-label="Password" @blur="$v.form.password.$touch" @keyup.enter="submit" :error="$v.form.password.$error" />
                <span class="form-group__message" v-if="!$v.form.password.minLength">A senha deve ter pelo menos {{ $v.form.password.$params.minLength.min }} letras.</span>
              </q-field>
            </form>
          </q-card-main>

          <q-card-separator/>

          <q-card-actions>
            <q-btn @click="login" type="submit">Login</q-btn>
          </q-card-actions>
        </q-card>
      </form>
    </div>
  </q-layout>
</template>

<script>
import {
  Toast, dom, event, openURL, QLayout, QToolbar, QToolbarTitle, QBtn, QIcon, QList, QListHeader, QItem, QItemSide, QItemMain, Qcard
} from 'quasar'
import { required, email, sameAs, minLength } from 'vuelidate/lib/validators'

const
  { viewport } = dom,
  { position } = event,
  moveForce = 30,
  rotateForce = 40,
  RAD_TO_DEG = 180 / Math.PI

function getRotationFromAccel(accelX, accelY, accelZ) {
  /* Reference: http://stackoverflow.com/questions/3755059/3d-accelerometer-calculate-the-orientation#answer-30195572 */
  const sign = accelZ > 0 ? 1 : -1
  const miu = 0.001

  return {
    roll: Math.atan2(accelY, sign * Math.sqrt(Math.pow(accelZ, 2) + miu * Math.pow(accelX, 2))) * RAD_TO_DEG,
    pitch: -Math.atan2(accelX, Math.sqrt(Math.pow(accelY, 2) + Math.pow(accelZ, 2))) * RAD_TO_DEG
  }
}

export default {
  name: 'index',
  components: {
    QLayout, QToolbar, QToolbarTitle, QBtn, QIcon, QList, QListHeader, QItem, QItemSide, QItemMain
  },
  data() {
    return {
      form: {
        email: '',
        password: ''

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
    position() {
      const transform = `rotateX(${this.rotateX}deg) rotateY(${this.rotateY}deg)`
      return {
        top: this.moveY + 'px',
        left: this.moveX + 'px',
        '-webkit-transform': transform,
        '-ms-transform': transform,
        transform
      }
    }
  },
  methods: {
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
      console.log(this.form.password + ' - ', this.form.email)
    }
  }
}
</script>
