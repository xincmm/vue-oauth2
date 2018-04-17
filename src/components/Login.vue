<template>
  <div>
    <p>登录界面</p>
    <form action="http://127.0.0.1:4000/api/oauth2/authorize?client_id=vue&response_type=code&redirect_uri=http://localhost:8080" method="POST">
      <input type="hidden" v-model="this.transactionID" name="transaction_id">
      <input v-model="username" placeholder="用户名" name="username">
      <input type="password" v-model="password" placeholder="密码" name="password">
      <button type="submit">提交</button>
    </form>
  </div>
</template>

<script>
import axios from 'axios'
axios.defaults.withCredentials = true

export default {
  name: 'login',
  data () {
    return ({
      username: '',
      password: '',
      transactionID: null,
      code: null,
      accessToken: null,
      getCodeURL: 'http://127.0.0.1:4000/api/oauth2/authorize?client_id=vue&response_type=code&redirect_uri=http://localhost:8080'
    })
  },
  created () {
    // 首先换取 transaction_id
    axios({
      method: 'get',
      url: this.getCodeURL,
      auth: {
        username: this.username,
        password: this.password
      }
    }).then((res) => {
      console.log(res.data)
      this.transactionID = res.data.transactionID
      console.log(res.data.transactionID)
    })
  }
}
</script>
