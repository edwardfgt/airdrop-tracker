<template>
  <div>
    <textarea v-model="inputText"></textarea>
    <button @click="handleSubmit">Submit</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputText: '',
      walletData: {}
    };
  },
  methods: {
    async fetchWalletData(wallet) {
      const url = `https://block-explorer-api.mainnet.zksync.io/api?module=account&action=txlist&page=1&offset=100&sort=desc&endblock=99999999&startblock=0&address=${wallet}`;
      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        return await response.json();
      } catch (error) {
        console.error("Fetch error: ", error);
      }
    },
    async handleSubmit() {
      const linesArray = this.inputText.split('\n');
      this.walletData = {};

      for (const wallet of linesArray) {
        if (wallet) {
          const data = await this.fetchWalletData(wallet);
          this.walletData[wallet] = data;
        }
      }

      console.log(this.walletData);
    }
  }
}
</script>
