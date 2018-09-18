<template>
  <div class="hello">
    <div v-if="!ready"><p>waiting...setup metamask.</p></div>
    <div v-if="ready">
      <p>connected in "{{ networkName }}"</p>
      <p>{{ address }}</p>
      <p>Balance of {{ balance }} ether.</p>
      <p>Send ether</p>
      to: <input type="text" placeholder="address" v-model="toAddress" />
      <input type="number" style="width:80px" v-model="toEth" />ether <button @click="sendTransaction">send tx</button>
      <p>{{ transactionMessage }}</p>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'HelloWorld',
  data () {
    return {
      web3js: undefined,
      address: "",
      networkName: "",
      balance: "",
      toAddress: "",
      toEth: 0,
      transactionMessage: ""
    }
  },
  computed: {
    ready: function(){
      return (this.web3js === undefined) ? false: true
    }
  },
  methods: {
    setup: function(){
      this.address = this.web3js.eth.accounts[0];
      this.web3js.eth.getBalance(this.address, (err, balance) =>
        this.balance = web3.fromWei(balance, "ether")
      );
      this.web3js.version.getNetwork((err, netId) => {
        switch (netId) {
          case "1":
            this.networkName = "mainnet"
            break
          case "2":
            this.networkName = "deprecated Morden test network"
            break
          case "3":
            this.networkName = "ropsten test network"
            break
          case "4":
            this.networkName = "rinkeby test network"
            break
          case "42":
            this.networkName = "kovan test network"
            break
          default:
            this.networkName = "unknown network"
        }
      });
    },
    sendTransaction: function(){
      this.web3js.eth.sendTransaction({
        from: this.address,
        to: this.toAddress,
        value: this.web3js.toWei(this.toEth, "ether")
      }, (err, transactionHash) => {
        if(err != undefined){
          this.transactionMessage = err.message;
          return;
        }
        this.transactionMessage = "success tx request: " + transactionHash;
      });
    }
  },
  mounted: function() {
    window.addEventListener('load', () => {
      if(typeof web3 !== 'undefined'){
        this.web3js = new Web3(web3.currentProvider);
        this.setup();
      } else {
        alert('No web3? You should consider trying MetaMask!');
      }
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
