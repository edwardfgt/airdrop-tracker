<template>
  <div>
    <textarea v-model="inputText"></textarea>
    <button @click="handleSubmit">Submit</button>

    <table class="wallet-table" v-if="Object.keys(walletData).length > 0">
      <thead>
        <tr>
          <th>Wallet</th>
          <th>Transactions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, wallet) in walletData" :key="wallet">
          <td>{{ wallet }}</td>
          <td>{{ data.result.length }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style>
.wallet-table {
  width: 100%;
  border-collapse: collapse;
}

.wallet-table th,
.wallet-table td {
  border: 1px solid #ccc;
  padding: 8px;
}

.wallet-table th {
  background-color: #f2f2f2;
}
</style>

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
