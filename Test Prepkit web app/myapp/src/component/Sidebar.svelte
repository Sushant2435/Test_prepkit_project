<script>
	import { choose_answer } from '.././store';
	import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';
	import Modal from './Modal.svelte';
	import { createEventDispatcher } from 'svelte';
	import { afterUpdate } from 'svelte';
	const dispatch = createEventDispatcher();
    export let show = false;
	let modal_show = false;
	export let sidebar_show;
	let question_array = [];
	onMount(async () => {
		const res = await fetch('lib/question.json');
		question_array = await res.json();
	});
	function changeQues(ques) {
		show = false;
		dispatch('displaySameQues', {
			data: ques,
			new_data: sidebar_show
		});
	}
	let attempted = 0;
	afterUpdate(() => {
		let data = $choose_answer.filter(Boolean);
		attempted = data.length;
	});
</script>

<main>
	{#if show}
		<nav class="mt-0" transition:fly={{ x: -500, opacity: 1 }}>
			<div class="btn-group position-absolute top-0 mt-2" role="group" aria-label="Basic example">
				<!-- <button type="button" class="btn btn-light  border px-5">All</button> -->
				<button type="button" class="btn btn-light border  px-5 me-5"
					>Attempted:<span class="text-primary ms-3 fw-bold">{attempted}</span></button
				>
				<button type="button" class="btn btn-light border  px-4"
					>Unattempted:<span class="text-warning ms-3 fw-bold">{question_array.length - attempted}</span
					></button
				>
			</div>
			<div>
				<div>
					{#each question_array as data, index}
						<!-- svelte-ignore a11y-click-events-have-key-events -->
						<div class=" mt-4" on:click={() => changeQues(index + 1)} style="cursor:pointer">
							<span class=" mr-3 ">{index + 1}.</span>
							{data.snippet}
						</div>
						<div class="float-end position">
							{#if $choose_answer[index] == null}
								<span class="badge bg-warning">Unattempted</span>
							{:else}
								<span class="badge bg-primary">Attempted</span>
							{/if}
						</div>
						<hr />
					{/each}
				</div>
			</div>
		</nav>
	{/if}
	<Modal bind:show={modal_show} />
</main>

<style>
	nav {
		bottom: 0;
		position: fixed;
		left: 0;
		height: 93%;
		padding: 2rem 1rem 0.6rem;
		border-right: 1px solid #aaa;
		background: #fff;
		overflow-y: auto;
		width: 30rem;
		background-color: rgb(235, 234, 234);
	}
	.position {
		position: relative;
		bottom: 22px;
	}
</style>
