<template>
<q-layout>
  <div slot="header" class="toolbar">
    <button @click="goToLists()">
      <i>arrow_back</i>
    </button>

    <q-toolbar-title :padding="2">
      Édition d'une liste
    </q-toolbar-title>
  </div>

  <div class="layout-view">
    <div class="layout-padding">
      <input v-model="name" placeholder="Nom de la liste" style="font-size:25px;">
      <br>
      <div class=" no-border">
      <draggable v-model="options" :options="{handle:'.my-handle'}" @start="drag=true" @end="drag=false">
        <div v-for="o,i in options" class="item two-lines">
          <i class="item-primary" @click="deleteItem(i)">delete</i>
          <div class="item-content">
            <input placeholder="choix" class="full-width" v-model="o.label">
          </div>
          <i class="my-handle item-secondary">drag_handle</i>
        </div>
      </draggable>

        <div class="item two-lines">
          <i class="item-primary">edit</i>
          <div class="item-content">
            <input placeholder="nouveau choix" class="full-width" v-model.lazy="newChoice">
          </div>
        </div>
      </div>
    </div>
  </div>
</q-layout>



</template>

<script>
import { LocalStorage, Dialog } from 'quasar'
import draggable from 'vuedraggable'

export default {
  components: {
    draggable
  },
  data () {
    return {
      name: '',
      initialName: '',
      options: [],
      newChoice: '',
      needSave: false
    }
  },
  watch: {
    newChoice () {
      this.addNewChoice()
    },
    options () {
      this.needSave = true
    },
    name () {
      this.needSave = true
    }
  },
  methods: {
    addNewChoice () {
      if (this.newChoice) {
        this.options.push({label: this.newChoice, count: 0})
        this.newChoice = ''
      }
    },
    goToLists () {
      let vm = this
      let route = {
        name: 'lists'
      }

      if (this.needSave) {
        this.addNewChoice()
        let lists = LocalStorage.get.item('listes')
        if (this.initialName) {
          // modify list
          delete lists[this.initialName]
          if (LocalStorage.get.item('currentList') === this.initialName) {
            LocalStorage.set('currentList', this.name)
          }
        }
        if (this.name) {
          lists[this.name] = this.options
          LocalStorage.set('listes', lists)
          this.$router.push(route)
        }
        else {
          console.log('hop')
          Dialog.create({
            title: 'Quel est le petit nom de cette liste ?',
            form: {
              name: {
                type: 'textbox',
                label: 'nom de la liste',
                model: ''
              }
            },
            buttons: [
              'Cancel',
              {
                label: 'Ok',
                handler (data) {
                  lists[data.name] = vm.options
                  LocalStorage.set('listes', lists)
                  vm.$router.push(route)
                }
              }
            ]
          })
        }
      }
      else {
        this.$router.push(route)
      }
    },
    deleteItem (index) {
      this.options.splice(index, 1)
      this.needSave = true
    }
  },
  mounted () {
    let lists = LocalStorage.get.item('listes')
    let listName = this.$route.params.listName
    if (listName in lists) {
      this.name = this.$route.params.listName
      this.initialName = this.name
      this.options = lists[listName]
    }
  }
}
</script>

<style lang="stylus">
</style>
