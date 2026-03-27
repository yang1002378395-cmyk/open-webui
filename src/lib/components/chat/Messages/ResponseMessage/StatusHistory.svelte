<script>
	import { getContext } from 'svelte';
	const i18n = getContext('i18n');

	import StatusItem from './StatusHistory/StatusItem.svelte';
	export let statusHistory = [];
	export let expand = false;
	export let messageDone = false;

	let showHistory = true;

	$: if (expand) {
		showHistory = true;
	} else {
		showHistory = false;
	}

	let history = [];
	let status = null;

	$: if (history && history.length > 0) {
		status = history.at(-1);
	}

	$: if (
		statusHistory.length !== history.length ||
		JSON.stringify(statusHistory) !== JSON.stringify(history)
	) {
		history = statusHistory;
	}

	// Check if all statuses are done but message is still in progress
	$: allStatusesDone = history.length > 0 && history.every((s) => s.done === true);
	$: showGeneratingIndicator = !messageDone && allStatusesDone;
</script>

{#if history && history.length > 0}
	{#if status?.hidden !== true}
		<div class="text-sm flex flex-col w-full">
			<button
				class="w-full"
				aria-label={$i18n.t('Toggle status history')}
				aria-expanded={showHistory}
				on:click={() => {
					showHistory = !showHistory;
				}}
			>
				<div class="flex items-start gap-2">
					<StatusItem {status} />
				</div>
			</button>

			{#if showHistory}
				<div class="flex flex-row">
					{#if history.length > 1}
						<div class="w-full">
							{#each history as status, idx}
								<div class="flex items-stretch gap-2 mb-1">
									<div class=" ">
										<div class="pt-3 px-1 mb-1.5">
											<span class="relative flex size-1.5 rounded-full justify-center items-center">
												<span
													class="relative inline-flex size-1.5 rounded-full bg-gray-500 dark:bg-gray-400"
												></span>
											</span>
										</div>
										{#if idx !== history.length - 1}
											<div
												class="w-[0.5px] ml-[6.5px] h-[calc(100%-14px)] bg-gray-300 dark:bg-gray-700"
											/>
										{/if}
									</div>

									<StatusItem {status} done={true} />
								</div>
							{/each}
						</div>
					{/if}
				</div>
			{/if}
		</div>
	{/if}
{/if}

{#if showGeneratingIndicator}
	<div class="text-sm flex flex-col w-full mt-1">
		<div class="status-description flex items-center gap-2 py-0.5 w-full text-left">
			<div class="flex flex-col justify-center -space-y-0.5">
				<div class="shimmer text-gray-500 dark:text-gray-500 text-base line-clamp-1 text-wrap">
					{$i18n.t('Generating response...')}
				</div>
			</div>
		</div>
	</div>
{/if}
