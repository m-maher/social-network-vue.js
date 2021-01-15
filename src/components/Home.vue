<template>
  <div>
    <Navbar v-bind:account="account" />
    <Main v-bind:posts="posts" v-bind:createPost="createPost" v-bind:tipPost="tipPost" />
  </div>
</template>

<script>

import Web3 from 'web3';
import SocialNetwork from '../abis/SocialNetwork.json'
import Main from './Main'
import Navbar from './Navbar.vue'

export default {
  components: {
    Main,
    Navbar
  },
  data() {
    return {
      account: '',
      socialNetwork: null,
      postCount: 0,
      posts: [],
      loading: true
    }
  },
  methods: {
  async loadBlockchainData() {
    const web3 = window.web3
    const accounts = await web3.eth.getAccounts()
    console.log(accounts)
    this.account = accounts[0]

    // Network ID
    const networkId = await web3.eth.net.getId()
    const networkData = SocialNetwork.networks[networkId]
    if (networkData) {
      this.socialNetwork = web3.eth.Contract(SocialNetwork.abi, networkData.address)
      this.postCount = await this.socialNetwork.methods.postCount().call()
      // Load Posts
      for (var i = 1; i <= this.postCount; i++) {
        const post = await this.socialNetwork.methods.posts(i).call()
        this.posts = [...this.posts, post]     
      }
      this.loading = false
    } else {
      window.alert('SocialNetwork contract not deployed to detected network.')
    }
  },
  async loadWeb3() {
    if (window.ethereum) {
      window.web3 = new Web3(window.ethereum)
      await window.ethereum.enable()
    }
    else if (window.web3) {
      window.web3 = new Web3(window.web3.currentProvider)
    }
    else {
      window.alert('Non-Ethereum browser detected. You should consider trying MetaMask!')
    }
  },
  createPost(content) {
    this.loading = true
    this.socialNetwork.methods.createPost(content).send({ from: this.account })
    .once('receipt', (receipt) => {
      this.loading = false
      console.log(receipt)
    })
  },
  tipPost(id, tipAmount) {
    this.loading = true
    this.socialNetwork.methods.tipPost(id).send({ from: this.account, value: tipAmount })
    .once('receipt', (receipt) => {
      this.loading = false;
      console.log(receipt)
    })
  }

  },
  async mounted() {
    await this.loadWeb3()
    await this.loadBlockchainData()
  }
}
</script>


