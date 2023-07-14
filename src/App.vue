<template>
  <div id="app" style="display: flex; flex-direction: column; align-items: center;">
    <img alt="Vue logo" src="./assets/logo.png" />
    <button @click="connect">Connect wallet</button>
    {{ account?.address }}
  </div>
</template>

<script>
import {
  EthereumClient,
  w3mConnectors,
  w3mProvider,
} from "@web3modal/ethereum";
import { Web3Modal } from "@web3modal/html";
import { configureChains, createConfig } from "@wagmi/core";
import { arbitrum, mainnet, polygon } from "@wagmi/core/chains";
import { watchAccount } from "@wagmi/core";

const projectId = "496c33146f0305590d925fe8cecab521";
export default {
  name: "App",
  data: function () {
    return {
      modal: null,
      account: null,
    };
  },
  async mounted() {
    const chains = [arbitrum, mainnet, polygon];
    const { publicClient } = configureChains(chains, [
      w3mProvider({ projectId }),
    ]);
    const wagmiConfig = createConfig({
      autoConnect: true,
      connectors: w3mConnectors({ projectId, chains }),
      publicClient,
    });
    const ethereumClient = new EthereumClient(wagmiConfig, chains);
    this.modal = await new Web3Modal({ projectId }, ethereumClient);
    watchAccount((account) => {
      console.log(account);
      this.account = account;
    });
  },
  methods: {
    connect: async function () {
      await this.modal.openModal();
    },
  },
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
