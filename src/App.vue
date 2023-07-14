<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <div v-if="account">
        {{ account }}
    </div>
    <button v-else @click="connect">Connect wallet</button>
  </div>
</template>

<script>
import { EthereumProvider } from "@walletconnect/ethereum-provider";
const projectId = "496c33146f0305590d925fe8cecab521";

export default {
  name: "App",
  data: function () {
    return {
      provider: null,
      account: null
    };
  },
  async mounted() {
    this.provider = await EthereumProvider.init({
      projectId,
      showQrModal: true,
      qrModalOptions: { themeMode: "light" },
      chains: [137, 1, 56],
      methods: ["eth_sendTransaction", "personal_sign"],
      events: [
        "chainChanged",
        "accountsChanged",
        "disconnect",
        "connect",
        "session_event",
        "display_uri",
      ],
      metadata: {
        name: "Dev Dapp",
        description: "My Dapp description",
        url: "https://my-dapp.com",
        icons: ["https://my-dapp.com/logo.png"],
      },
    });
    if (this.provider.accounts[0]) {
      this.account = this.provider.accounts[0];
    }
    console.log("provider", this.provider);
  },
  methods: {
    connect: async function () {
      try {
        if (this.provider) {
          await this.provider.enable();

          if (this.provider.accounts[0]) {
            this.account = this.provider.accounts[0];
          }
        }
      } catch (err) {
        console.error(err);
      } finally {
        console.log("finally");
      }
    },
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
