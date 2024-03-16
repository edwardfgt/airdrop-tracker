<template>
  <div class="p-4">
    <textarea v-model="inputText" class="w-full h-48 border border-gray-300 rounded-lg p-2"></textarea>
    <button @click="handleSubmit" class="mt-4 bg-blue-500 hover:bg-blue-600 text-white font-semibold py-2 px-4 rounded">Submit</button>

    <table class="wallet-table mt-4" v-if="Object.keys(walletData).length > 0">
      <thead>
        <tr>
          <th class="bg-gray-200 border border-gray-300 px-4 py-2">Zksync Wallets</th>
          <th class="bg-gray-200 border border-gray-300 px-4 py-2">Transactions</th>
          <th class="bg-gray-200 border border-gray-300 px-4 py-2">Total gas spent</th>
          <th class="bg-gray-200 border border-gray-300 px-4 py-2">Last Transaction</th>
          <th class="bg-gray-200 border border-gray-300 px-4 py-2">Wallet Age</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, wallet) in walletData" :key="wallet">
          <td class="border border-gray-300 px-4 py-2">{{ wallet }}</td>
          <td class="border border-gray-300 px-4 py-2">{{ data.result ? data.result.length : 0 }}</td>
          <td class="border border-gray-300 px-4 py-2">{{ data.totalUSDSpent }}</td>
          <td class="border border-gray-300 px-4 py-2">{{ getLastTransactionTimestamp(data.result) }}</td>
          <td class="border border-gray-300 px-4 py-2">{{ data.walletAge }}</td>
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
    async calculateTotalUSDSpent(data) {
      const ethPriceResponse = await fetch(`https://block-explorer-api.mainnet.zksync.io/api?module=stats&action=ethprice`);
      const ethPriceData = await ethPriceResponse.json();
      const ethPrice = parseFloat(ethPriceData.result.ethusd);

      let totalUSDSpent = 0;
      for (const transaction of data.result) {
        const gasUsed = parseInt(transaction.gasUsed);
        const gasPrice = parseInt(transaction.gasPrice);
        const gasFeesInEther = gasUsed * gasPrice / 1e18; // Convert to Ethereum
        const gasFeesInUSD = gasFeesInEther * ethPrice;
        totalUSDSpent += gasFeesInUSD;
      }
      return totalUSDSpent.toFixed(2);
    },
    getLastTransactionTimestamp(transactions) {
      if (!transactions || transactions.length === 0) {
        return "No transactions";
      }
      const timestamp = parseInt(transactions[0].timeStamp) * 1000; // Convert to milliseconds
      const lastTransactionDate = new Date(timestamp);
      const currentDate = new Date();

      const daysAgo = Math.floor((currentDate - lastTransactionDate) / (1000 * 60 * 60 * 24));

      if (daysAgo === 0) {
        return "today";
      } else {
        return `${daysAgo} day${daysAgo === 1 ? '' : 's'} ago`;
      }
    },
    getWalletAge(transactions) {
      if (!transactions || transactions.length === 0) {
        return "No interactions";
      }
      const firstTransactionTimestamp = parseInt(transactions[transactions.length - 1].timeStamp) * 1000; // Convert to milliseconds
      const firstTransactionDate = new Date(firstTransactionTimestamp);
      return firstTransactionDate.toLocaleDateString();
    },

    async handleSubmit() {
      const linesArray = this.inputText.split('\n');
      this.walletData = {};

      for (const wallet of linesArray) {
        if (wallet) {
          const data = await this.fetchWalletData(wallet);
          const totalUSDSpent = await this.calculateTotalUSDSpent(data);
          const walletAge = this.getWalletAge(data.result);
          this.walletData[wallet] = {
            result: data.result,
            totalUSDSpent: totalUSDSpent,
            walletAge: walletAge
          };
        }
      }
    }
  }
}
</script>
