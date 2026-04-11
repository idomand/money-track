<template>
  <div class="w-full px-4 py-6 sm:max-w-lg sm:mx-auto">
    <h1 class="text-xl sm:text-2xl font-bold mb-1">Slaa money</h1>
    <p class="text-sm text-gray-500 mb-4">{{ currentDate }}</p>

    <div
      class="mb-4 p-3 bg-blue-50 rounded-lg border border-blue-200 flex justify-between items-center"
    >
      <span class="font-semibold text-blue-800">Total:</span>
      <span class="text-blue-900 font-bold text-lg"
        >{{ grandTotal.toFixed(2) }} €</span
      >
    </div>

    <div class="overflow-x-auto">
      <table class="w-full border-collapse text-sm sm:text-base">
        <thead>
          <tr class="bg-gray-100">
            <th class="border border-gray-300 px-3 py-3 text-left">Type</th>
            <th class="border border-gray-300 px-3 py-3 text-right">Amount</th>
            <th class="border border-gray-300 px-3 py-3 text-right">Total</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="row in rowsWithTotal"
            :key="row.type"
            class="active:bg-gray-100"
          >
            <td class="border border-gray-300 px-3 py-3">{{ row.type }} €</td>
            <td class="border border-gray-300 px-3 py-3 text-right">
              {{ row.amount }}
            </td>
            <td class="border border-gray-300 px-3 py-3 text-right">
              {{ row.total.toFixed(2) }} €
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <button
      class="mt-6 w-full py-3 bg-green-600 text-white text-base font-medium rounded-xl active:bg-green-800 sm:hover:bg-green-700 transition-colors"
    >
      Add money
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      currentDate: new Date().toLocaleDateString("en-GB", {
        day: "numeric",
        month: "long",
        year: "numeric",
      }),
      rows: [
        { type: 0.01, amount: 3 },
        { type: 0.02, amount: 3 },
        { type: 0.05, amount: 10 },
        { type: 0.1, amount: 18 },
        { type: 0.2, amount: 103 },
        { type: 0.5, amount: 119 },
        { type: 1, amount: 16 },
        { type: 2, amount: 13 },
        { type: 5, amount: 8 },
        { type: 10, amount: 6 },
        { type: 20, amount: 1 },
        { type: 50, amount: 2 },
        { type: 100, amount: 0 },
      ],
    };
  },
  computed: {
    rowsWithTotal() {
      return this.rows.map((row) => ({
        ...row,
        total: parseFloat((row.type * row.amount).toFixed(2)),
      }));
    },
    grandTotal(): number {
      return this.rowsWithTotal.reduce((sum, row) => sum + row.total, 0);
    },
  },
});
</script>
