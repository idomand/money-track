<template>
  <div class="p-6 max-w-lg mx-auto">
    <h1 class="text-2xl font-bold mb-1">Slaa money</h1>
    <p class="text-gray-500 mb-4">{{ currentDate }}</p>

    <div class="mb-4 p-3 bg-blue-50 rounded-lg border border-blue-200">
      <span class="font-semibold text-blue-800">Total: </span>
      <span class="text-blue-900 font-bold">{{ grandTotal.toFixed(2) }} €</span>
    </div>

    <table class="w-full border-collapse">
      <thead>
        <tr class="bg-gray-100">
          <th class="border border-gray-300 px-4 py-2 text-left">
            Type of currency
          </th>
          <th class="border border-gray-300 px-4 py-2 text-right">Amount</th>
          <th class="border border-gray-300 px-4 py-2 text-right">Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rows" :key="row.type" class="hover:bg-gray-50">
          <td class="border border-gray-300 px-4 py-2">{{ row.type }} €</td>
          <td class="border border-gray-300 px-4 py-2 text-right">
            {{ row.amount }}
          </td>
          <td class="border border-gray-300 px-4 py-2 text-right">
            {{ row.total.toFixed(2) }} €
          </td>
        </tr>
      </tbody>
    </table>

    <button
      class="mt-6 px-5 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 active:bg-green-800 transition-colors"
    >
      Add money
    </button>
  </div>
</template>

<script setup lang="ts">
const currentDate = new Date().toLocaleDateString("en-GB", {
  day: "numeric",
  month: "long",
  year: "numeric",
});

const rows = [
  { type: 0.01, amount: 3, total: 0.03 },
  { type: 0.02, amount: 3, total: 0.06 },
  { type: 0.05, amount: 10, total: 0.5 },
  { type: 0.1, amount: 18, total: 1.8 },
  { type: 0.2, amount: 103, total: 20.6 },
  { type: 0.5, amount: 119, total: 59.5 },
  { type: 1, amount: 16, total: 16 },
  { type: 2, amount: 13, total: 26 },
  { type: 5, amount: 8, total: 40 },
  { type: 10, amount: 6, total: 60 },
  { type: 20, amount: 1, total: 20 },
  { type: 50, amount: 2, total: 100 },
  { type: 100, amount: 0, total: 0 },
];

const grandTotal = rows.reduce((sum, row) => sum + row.total, 0);
</script>
