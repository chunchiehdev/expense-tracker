<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expense="+expense" />
    <TransactionList :transactions="transactionsArray" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

const toast = useToast();


const transactions = ref(new Map());

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions && Array.isArray(savedTransactions)) {
    transactions.value = new Map(savedTransactions);
  }

})

const transactionsArray = computed(() => Array.from(transactions.value.entries()));



// Get total
const total = computed(() => {
  let sum = 0;
  for (let transaction of transactions.value.values()) {
    sum += transaction.amount;
  }
  return sum;
});

// Get income
const income = computed(() => {
  let sum = 0;
  for (let transaction of transactions.value.values()) {
    if (transaction.amount > 0) {
      sum += transaction.amount;
    }
  }
  return sum.toFixed(2);
});

// Get expense

const expense = computed(() => {
  let sum = 0;
  for (let transaction of transactions.value.values()) {
    if (transaction.amount < 0) {
      sum += transaction.amount;
    }
  }
  return sum.toFixed(2);
});

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.set(transactionData.id, {
    id: transactionData.id,
    text: transactionData.text,
    amount: transactionData.amount
  });
  updateLocalStorage();
  toast.success('Transaction added successfully');
};

const handleTransactionDeleted = (id) => {
  transactions.value.delete(id);
  updateLocalStorage();
  toast.success('Transaction deleted successfully');
};

const updateLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(Array.from(transactions.value.entries())));
}
</script>                                                                                                                                                           