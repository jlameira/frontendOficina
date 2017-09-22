<template>
  <q-layout ref="layout">
    <div slot="header" class="glossy">
      <q-toolbar-title :padding="1">
        {{repository.name}}
        <i>star</i>
        {{repository.stars}}
      </q-toolbar-title>
      <button @click="$router.push({name: 'root', params: {owner: $route.params.owner}})">
        <i>home</i>
      </button>
    </div>

    <div class="layout-view">
      <ul class="breadcrumb">
        <li>
          <a>
            <i>list</i> Lista de commits</a>
        </li>
      </ul>

      <div v-if="repository.commits.length > 0" class="list item-delimiter">
        <q-item class="card" v-for="(com, index) in repository.commits" :key="index">
          <q-card>
            <q-card-title>
              {{com.author}}
            </q-card-title>
            <q-card-main>
              {{com.message}}
            </q-card-main>
            <q-card-separator/>
          </q-card>

        </q-item>
      </div>

      <div v-if="repository.commits.length <= 0">
        <p>Sem commits</p>
      </div>
    </div>
  </q-layout>
</template>

<script>
import {
  Loading, Toast, QLayout, QToolbarTitle, QList,
  QListHeader,
  QItem,
  QItemSide,
  QItemMain,
  QCard,
  QCardSeparator,
  QCardTitle,
  QCardMain
} from 'quasar'
import { commits } from '../resources/github'

export default {
  data() {
    return {
      repository: {
        name: '',
        commits: []
      }
    }
  },
  created() {
    this.repository.name = this.$route.params.repository.full
    this.repository.stars = this.$route.params.repository.stars

    this.repository.commits = []
    Loading.show({ message: 'Procurando por commits' })

    commits(this.repository.name).then((result) => {
      result = result.data
      if (result.length > 0) {
        for (var i = 0; i < result.length; i++) {
          this.repository.commits.push({
            author: result[i].commit.author.name,
            message: result[i].commit.message
          })
        }
        Loading.hide()
      }
    }).catch(() => {
      Loading.hide()
      this.$refs.modalOwner.open()
      Toast.create({ html: 'Repositório não foi encontrado ou o tempo limite expirou' })
    })
  },
  components: {
    QLayout,
    QToolbarTitle,
    QList,
    QListHeader,
    QItem,
    QItemSide,
    QItemMain,
    QCard,
    QCardSeparator,
    QCardTitle,
    QCardMain
  }
}
</script>