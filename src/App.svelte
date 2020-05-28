<script>
  import { setContext } from "svelte";
  // components
  import Navbar from "./Navbar.svelte";
  import Expense from "./Expense.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Totals from "./Totals.svelte";
  import ExpenseForm from "./ExpenseForm.svelte";

  // data
  import expensesData from "./expenses";

  // variables
  let expenses = [...expensesData];
  // reactive
  $: isEditing = setId ? true : false;
  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);
  // set editing variables
  let setName = "";
  let setAmount = null;
  let setId = null;

  // toggle form variables
  let isFormOpen = false;
  // functions
  function showForm() {
    isFormOpen = true;
  }
  function removeExpense(id) {
    expenses = expenses.filter(item => item.id != id);
  }
  function clearExpenses() {
    expenses = [];
  }
  function addExpense({ name, amount }) {
    let expense = { id: Math.random() * Date.now(), name, amount };
    expenses = [expense, ...expenses];
  }
  function setModifiedExpense(id) {
    let expense = expenses.find(item => item.id === id);
    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;
  }
  function editExpense({ name, amount }) {
    expenses = expenses.map(item => {
      return item.id == setId ? { ...item, name, amount } : { ...item };
      setId = null;
      setAmount = null;
      setName = "";
    });
  }
  // context
  setContext("remove", removeExpense);
  setContext("modify", setModifiedExpense);
</script>

<!-- HTML -->
<Navbar {showForm} />

<main class="content">
  {#if isFormOpen}
    <ExpenseForm
      {addExpense}
      name={setName}
      amount={setAmount}
      {isEditing}
      {editExpense} />
  {/if}

  <Totals title="total expenses" {total} } />
  <ExpensesList {expenses} />
  <button
    type="button"
    class="btn btn-primary btn-block"
    on:click={clearExpenses}>
    clear expenses
  </button>
</main>
