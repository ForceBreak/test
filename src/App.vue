<template>
  <v-app id="app">
    <v-content>
      <v-container>
        <v-row justify-center class="align-center">
          <v-col lg="4">
            <v-text-field label="Put here your text" v-model.trim="text" />
          </v-col>
          <v-col lg="4">
            <v-btn @click="mostFrequentLetter()">Count</v-btn>
          </v-col>
          <v-col lg="4">
            <p>Most frequent letter: {{ textResult.character }}</p>
            <p>It repeats: {{ textResult.count }} times</p>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
      text: '',
      textResult: {
        letter: '',
        count: 0
      }
    }
  },
  methods: {
    mostFrequentLetter() {
      let characters = [] //Array for unique characters
      let textArray = this.text.replace(/ +/g, '').split('')
      textArray.forEach(elem => {
       let found = characters.findIndex(item => item.character == elem)
        if(found != -1){
          characters[found].count++
        }else{
          characters.push({character: elem, count: 1})
        }
      })
      
      // Find most counted character
      characters.forEach(e => {
        if(e.count > this.textResult.count){
          this.textResult = e 
        }
      }) 
    }
  }
}
</script>

<style>
</style>
