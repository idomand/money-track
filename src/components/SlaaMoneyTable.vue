<template>
  <div class="w-full px-4 py-6 sm:max-w-lg sm:mx-auto">
    <h1 class="text-xl sm:text-2xl font-bold mb-1">Slaa money</h1>
    <p class="text-sm text-gray-500 mb-4">{{ currentDate }}</p>

    <div
      class="mb-4 p-3 bg-blue-50 rounded-lg border border-blue-200 flex justify-between items-center"
    >
      <span class="font-semibold text-blue-800">Total:</span>
      <span class="text-blue-900 font-bold text-lg"
        >{{ (grandTotal + moneyInRoom).toFixed(2) }} €</span
      >
    </div>

    <input
      v-model="writeKey"
      type="password"
      placeholder="Enter group key to save data"
      class="w-full border border-gray-300 rounded-xl px-4 pt-3 pb-7 text-base focus:outline-none focus:ring-2 focus:ring-green-400"
    />
    <hr class="my-5" />

    <div class="overflow-x-auto">
      <section>
        <div class="flex justify-between items-center py-2 px-1 text-gray-600">
          <span>Money at room</span>
          <span>{{ moneyInRoom }} €</span>
        </div>

        <button
          @click="showDepositDialog = true"
          class="mt-2 w-full py-2 bg-blue-600 text-white text-sm font-medium rounded-xl active:bg-blue-800 sm:hover:bg-blue-700 transition-colors"
        >
          Add money
        </button>

        <div class="mt-2">
          <button
            @click="depositsOpen = !depositsOpen"
            class="flex items-center gap-1 text-sm text-gray-500 py-1"
          >
            <span>{{ depositsOpen ? "▾" : "▸" }}</span>
            <span>Weekly deposits ({{ weeklyDeposits.length }})</span>
          </button>
          <div
            v-if="depositsOpen"
            class="mt-1 border border-gray-200 rounded-lg overflow-hidden"
          >
            <div
              v-if="weeklyDeposits.length === 0"
              class="px-3 py-3 text-sm text-gray-400"
            >
              No deposits yet.
            </div>
            <div
              v-for="(deposit, i) in weeklyDeposits"
              :key="i"
              class="flex justify-between items-center px-3 py-2 text-sm border-b border-gray-100 last:border-0"
            >
              <span>{{
                new Date(deposit.date).toLocaleDateString("en-GB", {
                  day: "numeric",
                  month: "short",
                  year: "numeric",
                })
              }}</span>
              <span class="font-medium">{{ deposit.amount }} €</span>
            </div>
          </div>
        </div>
      </section>

      <hr class="my-5" />
      <section>
        <div class="flex justify-between items-center py-2 px-1 font-medium">
          <span>Money at treasurer</span>
          <span>{{ grandTotal.toFixed(2) }} €</span>
        </div>

        <table class="w-full border-collapse text-sm sm:text-base mt-2">
          <thead>
            <tr class="bg-gray-100">
              <th class="border border-gray-300 px-3 py-3 text-left">Type</th>
              <th class="border border-gray-300 px-3 py-3 text-right">
                Amount
              </th>
              <th class="border border-gray-300 px-3 py-3 text-right">Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="loading">
              <td
                colspan="3"
                class="border border-gray-300 px-3 py-6 text-center text-gray-400"
              >
                Loading...
              </td>
            </tr>
            <tr
              v-else
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
      </section>
    </div>
    <button
      @click="saveMoney"
      class="mt-6 w-full py-3 bg-green-600 text-white text-base font-medium rounded-xl active:bg-green-800 sm:hover:bg-green-700 transition-colors"
    >
      Add money
    </button>

    <div class="mt-4">
      <p v-if="saveError" class="mt-2 text-sm text-red-500">{{ saveError }}</p>
      <p v-if="saveSuccess" class="mt-2 text-sm text-green-600">
        Saved successfully.
      </p>
    </div>

    <!-- Deposit dialog -->
    <div
      v-if="showDepositDialog"
      class="fixed inset-0 bg-black/50 flex items-center justify-center z-50 px-4"
    >
      <div class="bg-white rounded-2xl w-full max-w-sm p-6">
        <h2 class="text-lg font-semibold mb-1">Add deposit</h2>
        <p class="text-sm text-gray-400 mb-4">{{ currentDate }}</p>
        <div class="mb-5">
          <label class="block text-sm text-gray-600 mb-1">Amount (€)</label>
          <input
            v-model.number="depositForm.amount"
            type="number"
            min="0"
            step="0.01"
            placeholder="0.00"
            class="w-full border border-gray-300 rounded-xl px-4 py-3 text-base focus:outline-none focus:ring-2 focus:ring-blue-400"
          />
        </div>
        <p v-if="depositError" class="mb-3 text-sm text-red-500">
          {{ depositError }}
        </p>
        <div class="flex gap-3">
          <button
            @click="showDepositDialog = false"
            class="flex-1 py-2 border border-gray-300 rounded-xl text-sm"
          >
            Cancel
          </button>
          <button
            @click="saveDeposit"
            class="flex-1 py-2 bg-blue-600 text-white rounded-xl text-sm font-medium active:bg-blue-800 sm:hover:bg-blue-700 transition-colors"
          >
            Save
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { db } from "../firebase";
import { ref, onValue, push } from "firebase/database";

interface MoneyRow {
  type: number;
  amount: number;
}

interface WeeklyDeposit {
  amount: number;
  date: number;
}

export default defineComponent({
  data() {
    return {
      currentDate: new Date().toLocaleDateString("en-GB", {
        day: "numeric",
        month: "long",
        year: "numeric",
      }),
      rows: [] as MoneyRow[],
      loading: true,
      weeklyDeposits: [] as WeeklyDeposit[],
      depositsOpen: false,
      showDepositDialog: false,
      depositForm: { amount: 0 },
      depositError: "",
      writeKey: "",
      saveError: "",
      saveSuccess: false,
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
    moneyInRoom(): number {
      return this.weeklyDeposits.reduce((sum, d) => sum + d.amount, 0);
    },
  },
  mounted() {
    const moneyRef = ref(db, "currentMoney/rows");
    onValue(moneyRef, (snapshot) => {
      this.loading = false;
      if (snapshot.exists()) {
        this.rows = snapshot.val();
      }
    });

    const depositsRef = ref(db, "weeklyDeposits");
    onValue(depositsRef, (snapshot) => {
      if (snapshot.exists()) {
        const val = snapshot.val();
        this.weeklyDeposits = Object.values(val) as WeeklyDeposit[];
      } else {
        this.weeklyDeposits = [];
      }
    });
  },
  methods: {
    async saveMoney() {
      this.saveError = "";
      this.saveSuccess = false;
      try {
        this.saveSuccess = true;
      } catch {
        this.saveError = "Permission denied.";
      }
    },
    async saveDeposit() {
      this.depositError = "";
      if (!this.depositForm.amount) {
        this.depositError = "Please fill in both fields.";
        return;
      }
      try {
        const depositsRef = ref(db, "weeklyDeposits");
        await push(depositsRef, {
          amount: this.depositForm.amount,
          date: Date.now(),
        });
        this.showDepositDialog = false;
        this.depositForm = { amount: 0 };
      } catch {
        this.depositError = "Permission denied.";
      }
    },
  },
});
</script>
