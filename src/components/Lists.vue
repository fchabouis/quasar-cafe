<template>
<q-layout>
  <div slot="header" class="toolbar">
    <button @click="goHome()">
      <i>arrow_back</i>
    </button>

    <q-toolbar-title :padding="2">
      Listes disponibles
    </q-toolbar-title>

  </div>

  <div class="layout-view">
    <div class="layout-padding">
      <div class="list no-border">

        <label class="item" v-for="listName in sortedLists">
          <div class="item-primary">
            <q-radio v-model="currentList" :val="listName"></q-radio>
          </div>
          <div class="item-content">
            {{ listName }}
            <i @click="goTo('/listEdition/'+listName)" style="font-size:20px;padding-left:5px;">edit</i>
            <i @click="showDialog(listName) "style="font-size:20px;padding-left:5px;">delete</i>
          </div>
        </label>
      </div>
    </div>

      <button 
        class="primary circular raised"
        @click="goTo('/listEdition/Nouvelle liste')"
        style="position: absolute; right: 18px; bottom: 18px;"
      >
        <i>add</i>
      </button>
    </div>

  </div>


</q-layout>
</template>

<script>
import { LocalStorage, Dialog } from 'quasar'

export default {
  data () {
    return {
      lists: LocalStorage.get.item('listes'),
      currentList: LocalStorage.get.item('currentList')
    }
  },
  watch: {
    currentList () {
      LocalStorage.set('currentList', this.currentList)
    }
  },
  methods: {
    goTo (path) {
      let route = {
        path: path
      }
      this.$router.push(route)
    },
    goHome () {
      let route = {
        path: '/'
      }
      this.$router.push(route)
    },
    showDialog (listName) {
      let vm = this
      Dialog.create({
        title: 'Vous êtes sûrs ?',
        message: 'Vous êtes sur le point de supprimer la liste ' + listName + '.',
        buttons: [
          'Annuler',
          {
            label: 'Supprimer',
            handler () {
              if (listName === vm.currentList) {
                vm.currentList = Object.keys(vm.lists)[0]
              }
              delete vm.lists[listName]
              // to trigger list display refresh
              vm.lists = Object.assign({}, {}, vm.lists)
              LocalStorage.set('listes', vm.lists)
            }
          }
        ]
      })
    }
  },
  computed: {
    sortedLists: function () {
      return Object.keys(this.lists).sort()
    }
  }
}
</script>

<style lang="stylus">
</style>
