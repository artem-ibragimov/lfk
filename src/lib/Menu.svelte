<script lang="ts">
	import { createEventDispatcher } from 'svelte';

	interface Data extends Record<string, boolean | Data> {}
	export let data: Data = {};
	export let resetFlag = false;

	$: if (resetFlag) {
		for (let v in values) {
			values[v] = false;
		}
	}

	const dispatcher = createEventDispatcher();
	const values: Record<string, boolean> = {};

	function oncheck(e: Event & { currentTarget: EventTarget & HTMLInputElement }) {
		dispatcher('select', [e.currentTarget.id, e.currentTarget.checked]);
	}
</script>

<div class="BodyParts">
	{#each Object.entries(data) as [summary, details]}
		{#if typeof details === 'boolean'}
			<div>
				<input id={summary} type="checkbox" bind:checked={values[summary]} on:change={oncheck} />
				<label for={summary}>{summary}</label>
			</div>
		{/if}
		{#if typeof details === 'object' && Object.keys(details).length !== 0}
			<details>
				<summary>{summary}</summary>
				{#each Object.entries(details) as [summary, details2]}
					{#if typeof details2 === 'boolean'}
						<div>
							<input
								id={summary}
								type="checkbox"
								bind:checked={values[summary]}
								on:change={oncheck}
							/>
							<label for={summary}>{summary}</label>
						</div>
					{/if}
					{#if typeof details2 === 'object' && Object.keys(details2).length !== 0}
						<details>
							<summary>{summary}</summary>
							{#each Object.entries(details2) as [summary, details3]}
								{#if typeof details3 === 'boolean'}
									<div>
										<input
											id={summary}
											type="checkbox"
											bind:checked={values[summary]}
											on:change={oncheck}
										/>
										<label for={summary}>{summary}</label>
									</div>
								{/if}
								{#if typeof details3 === 'object' && Object.keys(details3).length !== 0}
									<details>
										<summary>{summary}</summary>
									</details>
								{/if}
							{/each}
						</details>
					{/if}
				{/each}
			</details>
		{/if}
	{/each}
</div>

<style scoped>
	summary {
		cursor: pointer;
	}
	details {
		padding-left: 10px;
		display: flex;
		flex-direction: column;
	}
</style>
