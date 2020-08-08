<template>
  <div id="app">
    <div class="request-bar">
      <select v-model="method">
        <option>GET</option>
        <option>POST</option>
      </select>

      <input type="text" v-model="path"/>

      <button @click="sendRequest">Send</button>
    </div>

    <div class="body">
      <div class="sidebar">
        <ul><li @click="showKeysModal = !showKeysModal"><font-awesome-icon icon="key" /></li></ul>
      </div>
      <div class="result-container">
        <textarea v-model="requestBody" rows="38"></textarea>
        <code ref="requestResult"></code>
      </div>
    </div>

    <Modal :show="showKeysModal" title="API keys" subtitle="Set your Coinbase API keys" @close="showKeysModal = false">
      <label for="apiKey">API key</label>
      <input id="apiKey" v-model="apiKey" type="text" placeholder="API key" />

      <label for="apiSecret">API secret</label>
      <input id="apiSecret" v-model="apiSecret" type="text" placeholder="API secret" />
    </Modal>
  </div>
</template>

<script>
import crypto from 'crypto-js';
import axios from 'axios';
import Modal from "./components/Modal";

export default {
  name: 'App',
  components: {Modal},
  data(){
    return {
      method: "GET",
      path: "/v2/",
      requestBody: "",
      requestResult: "",
      apiKey: "",
      apiSecret: "",
      showKeysModal: false
    }
  },
  methods: {
    async sendRequest(){
      try{
      const response = await axios(this.buildRequest());
      this.setResponse(JSON.stringify(response.data, undefined, 2));
      }catch(err){
        this.setResponse(JSON.stringify(err.response.data), undefined, 2);
      }
    },
    setResponse(message){
      this.$refs.requestResult.innerText = message;
    },
    buildRequest(){
      const timestamp = Math.floor(Date.now() / 1000)
      const baseUrl = "https://api.coinbase.com"
      const message = `${timestamp}${this.method}${this.path}${this.requestBody}`

      const signature = crypto.HmacSHA256(message, this.apiSecret).toString(crypto.enc.Hex);
      
      return {
        method: this.method.toLowerCase(),
        url: `${baseUrl}${this.path}`,
        data: this.requestBody == "" ? "" : JSON.parse(this.requestBody),
        headers: {
          "CB-VERSION": "2017-11-11",
          "CB-ACCESS-KEY": this.apiKey,
          "CB-ACCESS-SIGN": signature,
          "CB-ACCESS-TIMESTAMP": timestamp
        }
      }
    }
  }
}
</script>

<style lang="scss">
:root{
  font-size: 62.5%;
  --primary-color: #ff6680;
}

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  outline: none;
  font-family: 'Nunito Sans', Helvetica, Arial, sans-serif;
}

body{
}

#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

label{
  font-size: 1.2rem;
}

label + input{
  margin-top: 0.4rem;
}

input {
  display: block;
  width: 100%;
  height: 4rem;
  padding: 0.8rem;
  border: 0;
  background: #f1f1f1;
  border-radius: 0.5rem;
}

button:hover{
  cursor: pointer;
}

#apiKey{
  margin-bottom: 2.4rem;
}

.request-bar{
  display: flex;
  border-bottom: 0.1rem solid #eee;
  padding: 0.8rem 1.6rem;

  select{
    border: 0;
    width: 10rem;
    padding: 0.8rem;
  }

  input {
    margin-left: 0.8rem;
    border-radius: 0;
    padding-left: 1.2rem;
  }

  button{
    width: 15rem;
    border: 0;
    background: var(--primary-color);
    border-radius: 0 0.3rem 0.3rem 0;
    color: white;
    font-weight: bold;

    &:hover{
      background: #ffb3c8;
    }
  }
}

.sidebar{
  padding: 1.6rem;
  width: 4.8rem;

  ul{
    list-style: none;
    font-size: 1.4rem;

    li{
      &:hover{
        cursor: pointer;
        color: var(--primary-color)
      }
    }
  }
}

.body{
  margin-top: 1.6rem;
  width: 100%;
  display: flex;
  justify-content: space-between;
}

.result-container{
  display: flex;
  justify-content: space-between;
  padding-right: 1.6rem;
  width: calc(100vw - 4.8rem);

  textarea, code{
    width: 50%;
    padding: 1.6rem;
    background: rgb(245, 245, 245);
  }

  textarea{
    resize: none;
    border: 0;
  }

  code{
    font-size: 1.2rem;
    white-space: pre-wrap;
    background: #171822;
    color: white;
    overflow: auto;
  }
}

@media screen and (max-width: 480px) {
  .result-container{
    display: flex;
    flex-direction: column;

    textarea,code{
      width: 100%;
      height: 43vh;
    }
  }
}
</style>
