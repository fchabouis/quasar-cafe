<template>
  <q-layout>
    <div class="layout-view">
      <div id="global">
        <div v-for="boisson in choices">
          <div class="row">

            <div class="width-2of5 text-right">
              <span class=""> {{ boisson.count ? boisson.count : '.' }} </span>
            </div>

            <div class="width-2of5 text-center labels">
              <span class="" @click="boisson.count += 1"> {{ boisson.label }} </span>
            </div>

            <div class="width-2of5 text-left">
              <button class="primary circular small" @click="boisson.count += 1">
                <i>add</i>
              </button>
              <button class="warning circular small" @click="boisson.count > 0 ? boisson.count -= 1 : 0">
                <i>remove</i>
              </button>
            </div>
          </div>
        </div>
      </div>

      <div>
      <div class="auto" id="total">
        <div class="auto text-center">
          <span class=""> {{ total }} participant{{total > 1 ? 's' : ''}}</span>
          <button class="negative circular small" @click="reset()">
            <i>delete</i>
          </button>
        </div>
      </div>  
      </div>
    </div>
  </q-layout>
</template>

<script>
export default {
  name: 'commande',
  data () {
    return {
      choices: [
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
      ]
    }
  },
  computed: {
    total () {
      let t = 0
      for (let i = 0; i < this.choices.length; ++i) {
        t += this.choices[i].count
      }
      return t
    }
  },
  methods: {
    reset () {
      for (let i = 0; i < this.choices.length; ++i) {
        this.choices[i].count = 0
      }
    }
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
  .row {
    padding-bottom: 10px;
  }
  .labels {
    vertical-align: top;
  }
</style>
