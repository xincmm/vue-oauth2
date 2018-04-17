<template>
  <div id="app">
    <router-link to="/login">登录</router-link>
    <router-view/>
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
      accessToken: null,
      code: null,
      beers: []
    }
  },
  created () {
    let parse = Query.parse(location.search)
    if (parse.code) {
      console.log(parse.code)
      this.code = parse.code
      this.getAccessToken()
    }
  },
  methods: {
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
