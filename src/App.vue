<template>
  <div class="min-h-screen bg-gray-50">
    <SlaaMoneyTable />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import SlaaMoneyTable from "./components/SlaaMoneyTable.vue";
export default defineComponent({
  name: "",
  components: { SlaaMoneyTable },
  data() {
    return {};
  },
  computed: {},
  methods: {},
});
</script>

<style scoped></style>
