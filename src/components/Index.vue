<template>
  <q-layout>
    <div slot="header" class="toolbar">
      <q-toolbar-title :padding="2">
        {{ currentList }}
      </q-toolbar-title>

      <button @click="goToLists()">
        <i>settings</i>
      </button>
    </div>
    <div class="layout-view" v-touch-swipe.horizontal="swipe">
      <div class="layout-padding">
        <div class="list no-border">
          <div v-for="boisson in choices" class="item">
            <div class="row">
              <div class="width-2of5 text-center">
                <q-numeric v-model="boisson.count"></q-numeric>
              </div>
              <div class="width-3of5 text-left onerow">
                <span v-bind:class="{ colored: boisson.count}">{{ boisson.label }} </span>
              </div>
            </div>
          </div>
        </div>
        <div>
          <div class="auto" id="total">
            <div class="auto text-center">
              <span class=""> Total : {{ total }}</span>
              <span id="deleteAll">
                <button class="negative circular small outline" @click="reset()">
                  <i>delete</i>
                </button>
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </q-layout>
</template>

<script>

import { LocalStorage, Dialog } from 'quasar'

export default {
  name: 'commande',
  data () {
    return {
      choices: [],
      currentList: ''
    }
  },
  computed: {
    total () {
      let t = 0
      if (this.choices) {
        for (let i = 0; i < this.choices.length; ++i) {
          t += this.choices[i].count
        }
      }
      return t
    },
    sortedLists: function () {
      return Object.keys(this.lists).sort()
    }
  },
  methods: {
    reset () {
      if (this.choices) {
        let vm = this
        Dialog.create({
          title: 'On recommence ?',
          message: 'Vous êtes sur le point de remettre les compteurs à zéro.',
          buttons: [
            'Annuler',
            {
              label: 'Ok',
              handler () {
                for (let i = 0; i < vm.choices.length; ++i) {
                  vm.choices[i].count = 0
                }
              }
            }
          ]
        })
      }
    },
    goToLists () {
      let route = {
        name: 'lists'
      }
      LocalStorage.set('listes', this.lists)
      this.$router.push(route)
    },
    swipe (obj) {
      if (obj.distance.x > 50) {
        let listsN = this.sortedLists.length
        let currentListIndex = this.sortedLists.indexOf(this.currentList)

        if (obj.direction === 'left' && currentListIndex < listsN - 1) {
          this.currentList = this.sortedLists[currentListIndex + 1]
          LocalStorage.set('currentList', this.currentList)
          this.choices = this.lists[this.currentList]
        }
        else if (obj.direction === 'right' && currentListIndex > 0) {
          this.currentList = this.sortedLists[currentListIndex - 1]
          LocalStorage.set('currentList', this.currentList)
          this.choices = this.lists[this.currentList]
        }
      }
    }
  },
  mounted () {
    // LocalStorage.clear()
    if (!LocalStorage.has('currentList') || !LocalStorage.has('listes')) {
      LocalStorage.set('currentList', 'boissons')
      LocalStorage.set('listes', {'boissons': [
        {label: 'café', count: 0},
        {label: 'déca', count: 0},
        {label: 'double', count: 0},
        {label: 'crême', count: 0},
        {label: 'café au lait', count: 0},
        {label: 'noisette', count: 0},
        {label: 'allongé', count: 0},
        {label: 'chocolat', count: 0},
        {label: 'thé', count: 0},
        {label: 'jus de fruits', count: 0}
      ]})
    }
    this.lists = LocalStorage.get.item('listes')
    this.currentList = LocalStorage.get.item('currentList')
    this.choices = this.lists[this.currentList]
  }
}
</script>

<style lang="stylus">
  #global {
    padding-top: 20px;
  }
  #total {
    padding-top: 30px;
  }
  .colored {
    color: #027be3;
    font-weight: bold;
  }
  .onerow {
    height: 2em;
    line-height: 2em;
  }
  #deleteAll {
    padding-left:10px;
  }
</style>
