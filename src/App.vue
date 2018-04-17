<template>
  <div id="app">
    <img src="./assets/logo.png">
    <!-- <router-view/> -->
    <a v-if="!accessToken" href="#" v-on:click="login">登录</a>
    <button @click="getToken">获取 Token</button>
    <button @click="getBeer">获取 Beer</button>
    <ul id="example-1">
      <li v-for="beer in beers" :key="beer.id">
        {{ beer.name }}
        {{ beer.type }}
        {{ beer.userId }}
      </li>
    </ul>
  </div>
</template>

<script>
import Query from 'query-string'
import axios from 'axios'

export default {
  name: 'App',
  data () {
    return {
      getCodeURL: 'http://localhost:4000/api/oauth2/authorize?client_id=vue&response_type=code&redirect_uri=http://localhost:8080',
      code: null,
      accessToken: null,
      beers: []
    }
  },
  created () {
    this.getCode()

    if (this.code) {
      this.getAccessToken()
    } else {
      this.accessToken = this.$cookie.get('accessToken')
    }
  },
  methods: {
    login () {
      this.$cookie.set('redirectURL', location.href, 1)
      location.href = this.getCodeURL
    },
    // 获取地址栏得 code
    getCode () {
      let parse = Query.parse(location.search)
      if (parse.code) {
        this.code = parse.code
      }
    },
    // 获取 token
    getAccessToken () {
      axios({
        method: 'post',
        url: 'http://localhost:4000/api/oauth2/token',
        auth: {
          username: 'vue',
          password: 'vue'
        },
        data: Query.stringify({
          code: this.code,
          grant_type: 'authorization_code',
          redirect_uri: 'http://localhost:8080'
        })
      }).then(res => {
        this.accessToken = res.data.access_token.value
        this.$cookie.set('accessToken', res.data.access_token.value, 30)
        location.href = '/'
      })
    },
    getToken () {
      console.log(this.accessToken)
    },
    getBeer () {
      axios({
        method: 'get',
        headers: {
          'Authorization': 'Bearer ' + this.accessToken
        },
        url: 'http://localhost:4000/api/beer'
      }).then(res => {
        console.log(res.data)
        this.beers = res.data
      }).catch(err => {
        console.log(err)
      })
    }
  }
}
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
