<script lang="ts">
	import Menu from '$lib/Menu.svelte';
	import { bodyPartsMenu, content, therapyMenu } from '$lib/data';
	import { tick } from 'svelte';

	const selected: Record<string, Record<string, boolean>> = {};
	$: console.debug(selected);

	let lastSelectedBodyPart = '';
	let resetFlag = false;

	function on_select_body_part(e: CustomEvent<[string, boolean]>) {
		const [name, checked] = e.detail;
		selected[name] = {};
		lastSelectedBodyPart = checked ? name : lastSelectedBodyPart;
		reset();
	}

	function reset() {
		resetFlag = true;
		tick().then(() => {
			resetFlag = false;
		});
	}

	function on_select_therapy(e: CustomEvent<[string, boolean]>) {
		const [name, checked] = e.detail;
		selected[lastSelectedBodyPart][name] = checked;
	}

	$: selected_content = Object.entries(selected)
		.map(([body_part, thereapies]) =>
			Object.entries(thereapies)
				.filter(([_, checked]) => checked)
				.map(([therapy, _]) => `${body_part}/${therapy}`)
		)
		.reduce((prev, cur) => prev.concat(cur), []);

	$: console.warn(selected_content);

	function toggle(e: MouseEvent & { currentTarget: EventTarget & HTMLDivElement }) {
		if (e.currentTarget.classList.contains('TextBlock-disabled')) {
			e.currentTarget.classList.remove('TextBlock-disabled');
		} else {
			e.currentTarget.classList.add('TextBlock-disabled');
		}
	}
</script>

<div class="container">
	<div class="sidebar noprint">
		<div class="menus">
			<Menu data={bodyPartsMenu} on:select={on_select_body_part} />
			{#if lastSelectedBodyPart}
				<Menu data={therapyMenu} on:select={on_select_therapy} {resetFlag} />
			{/if}
		</div>
		<button
			on:click={() => {
				window.print();
			}}>Печать</button
		>
	</div>
	<div class="content">
		{#each selected_content as val}
			{#if content[val]}
				{#each Object.entries(content[val]) as [title, text]}
					<div class="TextBlock TextBlock-disabled" on:click={toggle}>
						<h3>{title}</h3>
						{text}
					</div>
				{/each}
			{/if}
		{/each}
	</div>
</div>

<style>
	.container {
		display: flex;
	}
	.sidebar {
		flex: 1 1 50%;
		position: sticky;
		top: 0;
	}
	.menus {
		padding: 10px;
		display: flex;
		gap: 10px;
	}
	.content {
		flex: 2 1 50%;
		display: flex;
		flex-direction: column;
		gap: 10px;
	}
	.TextBlock {
		cursor: pointer;
		padding: 10px;
		border: 1px solid black;
	}

	.TextBlock-disabled {
		opacity: 0.7;
		border: none;
	}

	@media print {
		.noprint {
			display: none;
		}
		.TextBlock {
			padding: 10px;
			border: none;
		}
		.TextBlock-disabled {
			display: none;
		}
	}
</style>
