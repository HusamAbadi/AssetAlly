<script>
	export let item;
	export let DeleteItem;
	export let index;
</script>

<tr>
	<td class={item.type === 'Expense' ? 'expense' : 'revenue'}>{item.type}</td>
	<td>{item.name}</td>
	<td>{item.amount}€</td>
	<td>
		<button on:click={() => DeleteItem(index)}>Delete</button>
	</td>
</tr>
