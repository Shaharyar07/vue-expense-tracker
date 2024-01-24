<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="income" :expense="expense" />
    <TransactionList
      :transactions="transactions"
      @deleteTransaction="HandleDelete"
    />
    <AddTransaction @addTransaction="HandleTransaction" />
  </div>
</template>
<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";
import { ref, computed, onMounted } from "vue";
import { useToast } from "vue-toastification";
const toast = useToast();

const transactions = ref([]);

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, item) => (acc += item.amount), 0);
});

//Get Income
const income = computed(() => {
  return transactions.value
    .filter((item) => item.amount > 0)
    .reduce((acc, item) => (acc += item.amount), 0);
});

//Get Expense
const expense = computed(() => {
  return transactions.value
    .filter((item) => item.amount < 0)
    .reduce((acc, item) => (acc += item.amount), 0);
});

// Handle Transaction
const HandleTransaction = (transaction) => {
  console.log(transaction);
  transactions.value.push({
    id: Math.floor(Math.random() * 100000000),
    text: transaction.text,
    amount: transaction.amount,
  });
  saveTransactions();
  toast.success("Transaction Added");
};

// Handle Delete
const HandleDelete = (id) => {
  transactions.value = transactions.value.filter((item) => item.id !== id);
  saveTransactions();
  toast.success("Transaction Deleted");
};

//get transactions from local storage on Mount

onMounted(() => {
  const localTransactions = JSON.parse(localStorage.getItem("transactions"));
  if (localTransactions) {
    transactions.value = localTransactions;
  }
});

//save transactions to local storage on handle transaction
const saveTransactions = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
