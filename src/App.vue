<template>
  <div id="app" class="wrapper">
    <div class="is-flex">
      <section class="sidebar">
        <img class="logo" src="@/assets/eth-diamond-rainbow.png">
      </section>
      <main>
        <header class="margin-bottom">
          <h1>Greeting Smart Contract</h1>
          <h2 v-show="isShowingCurrentGreetingValue">{{ currentGreetingValue }}</h2>
        </header>
        <div class="margin-bottom">
          <button @click="fetchGreeting">Fetch Greeting</button>
        </div>
        <div>
          <input type="text" v-model="greetingValue" placeholder="Set greeting" />    
          <button class="margin-left" @click="setGreeting">Set Greeting</button>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
import { ethers } from 'ethers'
import Greeter from './artifacts/contracts/Greeter.sol/Greeter.json'

const greeterAddress = "0xMY_CONTRACT_ADDRESS_HERE"

export default {
  name: 'GreetingSmartContract',
  data () {
    return {
      currentGreetingValue: '',
      greetingValue: ''
    }
  },
  computed: {
    isShowingCurrentGreetingValue () {
      return this.currentGreetingValue !== ''
    }
  },
  methods: {
    async requestAccount () {
      await window.ethereum.request({ method: 'eth_requestAccounts' })
    },
    async fetchGreeting () {
      if (!window.ethereum || typeof window.ethereum === 'undefined') return
      
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      const contract = new ethers.Contract(greeterAddress, Greeter.abi, provider)
      
      try {
        const data = await contract.greet()
        this.currentGreetingValue = data
      } catch (err) {
        console.log("Error: ", err)
      }
    },
    async setGreeting () {
      if (!this.greetingValue) return
      if (!window.ethereum || typeof window.ethereum === 'undefined') return
      
      await this.requestAccount()
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner()
      const contract = new ethers.Contract(greeterAddress, Greeter.abi, signer)
      const transaction = await contract.setGreeting(this.greetingValue)
      await transaction.wait()
      this.fetchGreeting()
    }
  }
}
</script>

<style>
html, body {
  font-family: monospace;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #bbb;
}
h1 {
  margin-top: 0;
}
input {
  padding: .875rem;
}
button {
  padding: 1rem 1.5rem;
}
button {
  border: 0;
  border-radius: 4px;
  box-shadow: none;
  background-color: #f60
}
.wrapper {
  padding: 4rem;
}
.is-flex {
  display: flex;
  justify-content: center;
}
.margin-bottom {
  margin-bottom: 1.5rem;
}
.margin-left {
  margin-left: 1.5rem;
}
.sidebar {
  padding-right: 4rem;
}
.logo {
  max-width: 200px;
}
</style>
