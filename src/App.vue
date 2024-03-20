<template>
  <div class="p-4">
    <wallet-input @submit="handleSubmit"></wallet-input>
    <wallet-table :walletData="walletData"></wallet-table>
  </div>
</template>

<script>
import WalletInput from './components/WalletInput.vue';
import WalletTable from './components/WalletTable.vue';

export default {
  components: {
    WalletInput,
    WalletTable
  },
  data() {
    return {
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

    async handleSubmit(inputText) {
      const linesArray = inputText.split('\n');
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
};
</script>
