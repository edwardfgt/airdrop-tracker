<template>
    <tr>
      <td class="border border-gray-300 px-4 py-2">{{ wallet }}</td>
      <td class="border border-gray-300 px-4 py-2">{{ data.result ? data.result.length : 0 }}</td>
      <td class="border border-gray-300 px-4 py-2">{{ data.totalUSDSpent }}</td>
      <td class="border border-gray-300 px-4 py-2">{{ getLastTransactionTimestamp(data.result) }}</td>
      <td class="border border-gray-300 px-4 py-2">{{ data.walletAge }}</td>
    </tr>
  </template>
  
  <script>
  export default {
    props: {
      wallet: String,
      data: Object
    },
    methods: {
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
    }
  };
  </script>
  