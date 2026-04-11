<template>
  <div class="min-h-screen bg-gray-50">
    <SlaaMoneyTable />
    <KnownExpenses />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import SlaaMoneyTable from "./components/SlaaMoneyTable.vue";
import KnownExpenses from "./components/KnownExpenses.vue";
export default defineComponent({
  name: "",
  components: { SlaaMoneyTable, KnownExpenses },
  data() {
    return {};
  },
  computed: {},
  methods: {},
});
</script>

<style scoped></style>
