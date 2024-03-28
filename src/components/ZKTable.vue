<template>
    <div v-if="Object.keys(walletData).length > 0">
      <table class="wallet-table mt-4">
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
          <wallet-row v-for="(data, wallet) in walletData" :key="wallet" :wallet="wallet" :data="data"></wallet-row>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  import WalletRow from './ZKRow.vue';
  
  export default {
    components: {
      WalletRow
    },
    props: {
      walletData: Object
    }
  };
  </script>
  