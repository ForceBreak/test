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
            <p>Most frequent character: {{ textResult.character }}</p>
            <p>It repeats: {{ textResult.count }} times</p>
          </v-col>
        </v-row>
        <v-row>
          <v-col lg="8">
            <v-btn @click="paramsToObject()">Get params object</v-btn>
            {{ urlObject }}
          </v-col>
          <v-col lg="4">
            <p :class="isEven" v-if="formStorageText">{{formStorageText}}</p>
            <v-form
                ref="form"
                v-model="valid"
                lazy-validation
              >
                <v-text-field
                  v-model="formText"
                  :rules="[numberRule.number]"
                  label="LocalStorage text"
                  required
                />

                <v-btn
                  :disabled="!valid"
                  color="success"
                  class="mr-4"
                  @click="set"
                >
                  Set
                </v-btn>

                <v-btn
                  color="error"
                  class="mr-4"
                  @click="clear"
                >
                  Clear
                </v-btn>
              </v-form>
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
      valid: true,
      formStorageText: '',
      formText: '',
      text: '',
      textResult: {
        letter: '',
        count: 0
      },
      urlObject: {},
      numberRule: {
        number: value => {
          const pattern = /^\d+$/ 
          return pattern.test(value) || 'Only numbers'
        }
      },
      keyName: 'num23'
    }
  },
  methods: {
    mostFrequentLetter() {
      this.textResult = {
        letter: '',
        count: 0
      }
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
    },
    paramsToObject() {
      const urlParams = new URLSearchParams(window.location.search.substr(1));
      const entries = urlParams.entries();
      let result = {}
      for(let entry of entries) {
        const [key, value] = entry;
        result[key] = value;
      }
      
      this.urlObject = result;
    },
    clear() {
      this.formText = ''
      localStorage.removeItem(this.keyName)
    },
    set() {
      localStorage.setItem(this.keyName, this.formText)
    }
  },
  computed:{
    isEven() {
      return Number(this.formStorageText) % 2 == 0 ? 'even' : 'odd'
    }
  },
  mounted() {
    this.formStorageText = localStorage.getItem(this.keyName)
  }
}
</script>

<style>
.even {
  color: green;
}
.odd{
  color: red;
}
</style>
