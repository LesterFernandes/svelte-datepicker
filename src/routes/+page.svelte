<script lang="ts">
	import cn from 'classnames';
	import { DateTime } from 'luxon';
	import { afterUpdate, onMount } from 'svelte';
	import '../app.css';

	const dayz = ['mon', 'tue', 'wed', 'thur', 'fri', 'sat', 'sun'];
	let cells: (number | string)[] = Array(35).fill('');

	let date = DateTime.now();
	let selDayIndex: number;

	afterUpdate(() => {
		const startOfMonth = date.startOf('month');
		const endOfMonth = date.endOf('month');
		const startIndex = Number(startOfMonth.toFormat('E')) - 1;
		const endIndex = 28 + Number(endOfMonth.toFormat('E')) - 1;
		let k = 0;
		let cou = 1;
		const cellz = Array(35).fill('');
		while (k < 35) {
			if (k >= startIndex && k <= endIndex) {
				cellz[k] = cou
				cou += 1;
			}
			k += 1;
		}
		cells = [...cellz];
	});

	function prev() {
		date = date.minus({ months: 1 }).startOf('month');
		selDayIndex = -1
	}

	function next() {
		date = date.plus({ months: 1 }).startOf('month');
		selDayIndex = -1
	}

	function onDateClick(selectedDayIndex: number) {
		selDayIndex = selectedDayIndex;
	}
</script>

<div class="container mx-auto p-20">
	<p>{date.toFormat('dd, MMM yyyy')}</p>
	<div class="flex flex-row">
		{#each dayz as day}
			<div class="w-10 h-7 italic">{day}</div>
		{/each}
	</div>
	{#each Array(5).fill(null) as _, ri}
		<div class="flex flex-row">
			{#each dayz as day, ci}
				{@const dayIndex = 7 * ri + ci}
				<!-- svelte-ignore a11y-click-events-have-key-events -->
				<!-- svelte-ignore a11y-no-static-element-interactions -->
				<div
					on:click={function () {
                        if (cells[dayIndex] == '') return false
                        date = date.set({day: Number(cells[dayIndex])})
						onDateClick(dayIndex);
					}}
					class={cn(
						'w-10 h-10 border-2 hover:bg-sky-600 hover:text-white flex justify-center items-center',
						{
							'bg-sky-700': (dayIndex === selDayIndex && cells[dayIndex] !== ''),
							'text-white': dayIndex === selDayIndex,
							'pointer-events-none': cells[dayIndex] == null,
							'cursor-pointer': Number.isInteger(cells[dayIndex])
						}
					)}
				>
					{cells[dayIndex]}
				</div>
			{/each}
		</div>
	{/each}
	<button class="hover:underline" on:click={prev}>prev</button>
	<button class="hover:underline" on:click={next}>next</button>
</div>

<style lang="postcss">
</style>
