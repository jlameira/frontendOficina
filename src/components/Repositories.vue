<template>
  <q-layout ref="layout">
    <div slot="header" class="glossy">
      <q-toolbar-title :padding="1">
        {{owner.name}}
      </q-toolbar-title>
      <button @click="$refs.modalOwner.open()">
        <i>build</i>
      </button>
    </div>

    <div class="layout-view">
      <ul class="breadcrumb">
        <li>
          <a>
            <i>list</i> Lista de repositórios</a>
        </li>
      </ul>

      <div v-if="owner.repositories.length > 0">
        <q-list highlight inset-separator link>
          <q-item v-for="(rep,index) in owner.repositories" :key="index" @click="details(rep)">
            <q-item-main>
              {{rep.name}}
              <q-item-side right>
                <span>
                  <i>star</i> {{rep.stars}}</span>
              </q-item-side>
            </q-item-main>
          </q-item>
        </q-list>
      </div>

      <div v-if="owner.repositories.length <= 0">
        <p>Sem repositórios</p>
      </div>
    </div>

    <q-modal ref="modalOwner" position="right" :content-css="{padding: '30px'}">
      <strong>Trocar de organização</strong>
      <hr>
      <div class="floating-label">
        <input required v-model="owner.link" class="full-width">
        <label>Nome no link do GitHub</label>
        <button class="primary small full-width" @click="register()">
          Pesquisar repositórios
        </button>
      </div>
    </q-modal>
  </q-layout>
</template>

<script>
import {
  dom,
  event,
  openURL,
  QLayout,
  QToolbar,
  QToolbarTitle,
  QBtn,
  QIcon,
  QList,
  QListHeader,
  QItem,
  QItemSide,
  QItemMain,
  QModal,
  QItemSeparator
} from 'quasar'
import { Loading, Toast } from 'quasar'
import { repositories } from '../resources/github'

export default {
  name: 'index',
  data() {
    return {
      owner: {
        name: 'Sem organização selecionada',
        link: '',
        repositories: []
      }
    }
  },
  created() {
    if ('owner' in this.$route.params) {
      this.owner.link = this.$route.params.owner
      setTimeout(() => {
        this.register()
      }, 500)
    }
  },
  methods: {
    register() {
      if (this.owner.link !== '') {
        this.owner.repositories = []
        this.$refs.modalOwner.close()
        Loading.show({ message: 'Procurando repositórios' })

        repositories(this.owner.link).then((result) => {
          result = result.data
          if (result.length > 0) {
            this.owner.name = result[0].owner.login
            for (var i = 0; i < result.length; i++) {
              this.owner.repositories.push({
                name: result[i].name,
                stars: result[i].stargazers_count,
                full: result[i].full_name
              })
            }
            Loading.hide()
          }
        }).catch(() => {
          Loading.hide()
          this.$refs.modalOwner.open()
          Toast.create({ html: 'Organização não foi encontrada ou o tempo limite expirou' })
        })
      }
    },
    details(r) {
      this.$router.push({ name: 'details', params: { repository: r, owner: this.owner.link } })
    }
  },
  components: {
    QLayout,
    QToolbar,
    QToolbarTitle,
    QBtn,
    QIcon,
    QList,
    QListHeader,
    QItem,
    QItemSide,
    QItemMain,
    QModal,
    QItemSeparator
  },
}
</script>