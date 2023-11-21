<script>
	import { onMount } from 'svelte';
	import Item from '../components/Item.svelte';
	let item = [];
	let items = [];
	let itemName = '';
	let type;
	let equals = 0;

	let amount;
	let net = 0;
	let active = 'All';
	let isDarkMode = false;
	let exchangeRates = {};

	async function fetchData() {
		try {
			const response = await fetch('https://api.exchangerate-api.com/v4/latest/euro');
			const data = await response.json();
			exchangeRates = data.rates;
		} catch (error) {
			console.error('Error fetching data:', error);
		}
	}

	function addItem() {
		const numericAmount = parseFloat(amount);

		if (itemName == '' || amount == 0) {
			alert('Please enter a name');
		} else {
			item = {
				name: itemName,
				amount: numericAmount,
				type: type
			};
			if (type === 'Expense') net -= numericAmount;
			else if (type === 'Revenue') net += numericAmount;
			items = [...items, item];
			itemName = '';
			amount = 0;
			equals = net * parseFloat(exchangeRates['HUF']);
		}
	}

	function DeleteItem(index) {
		console.log(index);
		items = items.filter((_, i) => i !== index);
	}

	function activate(type) {
		active = type;
	}

	function toggleDarkMode() {
		isDarkMode = !isDarkMode;
		document.body.classList.toggle('dark-mode', isDarkMode);
	}

	onMount(() => {
		fetchData();
	});
</script>

<div>
	<h1>AssetAlly</h1>
	<button on:click={toggleDarkMode}>{isDarkMode ? 'Light Mode ☀️' : 'Dark Mode 🌑'}</button>

	<div class="header">
		<div class="inputs">
			<select bind:value={type}>
				<option selected value="Expense">Expense</option>
				<option value="Revenue">Revenue</option>
			</select>
			<input
				on:change={(e) => (itemName = e.target.value)}
				type="text"
				placeholder="What's the item?"
				required
				value={itemName}
			/>

			<input
				on:change={(e) => (amount = e.target.value)}
				type="number"
				placeholder="How much is it in Euros?"
				required
				value={amount}
			/>
			<button class="add-cta" on:click={addItem}>➕</button>
		</div>
		<div class="toggle">
			<button class={active === 'Expense' ? 'active' : ''} on:click={() => activate('Expense')}
				>Expenses</button
			>
			<button class={active === 'Revenue' ? 'active' : ''} on:click={() => activate('Revenue')}
				>Revenues</button
			>
			<button class={active === 'All' ? 'active' : ''} on:click={() => activate('All')}>All</button>
		</div>
	</div>

	<div class="items-container">
		<table>
			{#each items as item, index}
				{#if item.type === active || active === 'All'}
					<Item {item} {DeleteItem} {index} />
				{/if}
			{/each}
		</table>
		<div class="net-box">
			<p class="net">Net: {net}€</p>
			<p class="equals">{equals} FT</p>
		</div>
		<br />
		<br />
		{#if Object.keys(exchangeRates).length > 0}
			<ul>
				{#each Object.entries(exchangeRates) as [currency, rate]}
					{#if currency === 'EUR' || currency === 'USD' || currency === 'HUF'}
						<li>{currency}: {rate}</li>
					{/if}
				{/each}
			</ul>
		{/if}
		<!-- <button on:click={fetchData}>Rate</button> -->
	</div>
</div>
