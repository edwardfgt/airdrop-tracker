<template>
  <div class="p-4">
    <textarea v-model="inputText" class="w-full h-48 border border-gray-300 rounded-lg p-2"></textarea>
    <button @click="handleSubmit" class="mt-4 bg-blue-500 hover:bg-blue-600 text-white font-semibold py-2 px-4 rounded">Submit</button>

    <table class="wallet-table mt-4" v-if="Object.keys(walletData).length > 0">
      <thead>
        <tr>
          <th class="bg-gray-200 border border-gray-300 px-4 py-2">Wallet</th>
          <th class="bg-gray-200 border border-gray-300 px-4 py-2">Transactions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, wallet) in walletData" :key="wallet">
          <td class="border border-gray-300 px-4 py-2">{{ wallet }}</td>
          <td class="border border-gray-300 px-4 py-2">{{ data.result.length }}</td>
        </tr>
      </tbody>
    </table>
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
      const url = `https://block-explorer-api.mainnet.zksync.io/api?module=account&action=txlist&page=1&offset=1000&sort=desc&endblock=99999999&startblock=0&address=${wallet}`;
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
          console.log(data); // Log fetched data for inspection
        }
      }

      console.log(this.walletData);
    }
  }
}
</script>
