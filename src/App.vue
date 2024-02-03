<script setup>
  import { ref, computed, onMounted } from 'vue';
  import { useToast } from 'vue-toastification';

  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';

  const toast = useToast();

  const transactions = ref([]);

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

    if (savedTransactions) {
      transactions.value = savedTransactions;
    }
  })

  // Get total
  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0);
  });

  // Get income
  const income = computed(() => {
    return transactions.value
    .filter(item => item.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2)
  });

  // Get expense
  const expenses = computed(() => {
    return transactions.value
    .filter(item => item.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2)
  });

  // Add transaction
  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: transactions.value.length + 1,
      text: transactionData.text,
      amount: transactionData.amount
    })

    savedTransactionsToLocalStorage();

    toast.success('Transaction added');
  }

  // Delete transaction
  const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter(item => item.id !== id);

    savedTransactionsToLocalStorage();

    toast.success('Transaction deleted')
  }

  // Save to localstorage
  const savedTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
  }

</script>

<template>
  <Header />

  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>  
</template>

<style scoped>
</style>
