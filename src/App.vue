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
        <v-row>
          <v-col lg="3" v-for="item in list" :key="item.id">
            <v-card class="mx-auto text-center">
              <v-card-subtitle class="pb-0 pt-2">
                #{{ item.id }}
              </v-card-subtitle>
              <v-card-subtitle class="pb-0 pt-0">
                {{ item.title }}
              </v-card-subtitle>
              <v-card-subtitle class="pb-4 pt-0">
                {{ item.sku }}
              </v-card-subtitle>

              <v-img
                class="white--text align-end"
                height="200px"
                :src="item.image"
              />

              <v-card-title class="justify-center subtitle-2 font-weight-bold pb-0">
                PRODUCT OPTIONS:
              </v-card-title>

              <v-card-text class="text--primary text-left pb-0">
                <v-list-item v-for="(elem, index) in item.options" :key="index">
                  <v-list-item-content >
                    <v-list-item-subtitle>
                      Metal type: {{ elem.metal_type }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle>
                      Metal color: {{ elem.metal_color }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle>
                      Stone shape: {{ elem.stone_shape }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle>
                      Gemstone color: {{ elem.gemstone_color }}
                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </v-card-text>

              <v-card-title class="justify-center title font-weight-bold pt-0">
                {{ formatPrice(item.price) }} {{ item.currency }}
              </v-card-title>
            </v-card>
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
      keyName: 'num23',
      list: []
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
    },
    getList() {
      this.$http.get('http://54.39.188.42')
      .then(res => {
        let decode = atob(res.data)
        let parseDecoded = JSON.parse(decode)
        this.list = [...parseDecoded]
        console.log(this.list)
      })
    },
    formatPrice(price) {
      return parseFloat(Number(price).toFixed(2))
    }
  },
  computed:{
    isEven() {
      return Number(this.formStorageText) % 2 == 0 ? 'even' : 'odd'
    }
  },
  mounted() {
    this.formStorageText = localStorage.getItem(this.keyName)
    this.getList()
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
.title{
  width: 100%;
  text-align: center;
}
</style>
