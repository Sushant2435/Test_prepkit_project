<script>
	import { choose_answer } from '../store';
	import { onMount } from 'svelte';
	import { tweened } from 'svelte/motion';
	import Sidebar from './Sidebar.svelte';
	let dialog;
	let questionArray = [];
	let countValue = 1;
	onMount(async () => {
		const res = await fetch('lib/question.json');
		questionArray = await res.json();
		$choose_answer = checked__opt;
		dialog = document.getElementById('confirmation-dialog');
		changePageUrl(countValue);
	});
	function quesDrop(event) {
		countValue = event.detail.data;
		changePageUrl(countValue);
		
	}
	let checked__opt = [];
	const showDialogClick = (asModal = true) => {
		try {
			dialog[asModal ? 'showModal' : 'show']();
		} catch (e) {}
	};
	const closeClick = () => {
		dialog.close();
	};
	// timer
	let original = 5 * 60;
	let timer = tweened(original);
	setInterval(() => {
		if ($timer >1) $timer--;
	}, 1000);
	$: minutes = Math.floor($timer / 60);
	$: seconds = Math.floor($timer - minutes * 60);
	$: sidebar_show = false;
	function switchQuestion(event) {
		if (event == 'previous') {
			countValue = countValue - 1;
			changePageUrl(countValue);
		} else {
			countValue = countValue + 1;
			changePageUrl(countValue);
		}
	}
	function changePageUrl(countValue) {
		history.pushState('', '', location.origin + '/testpage?qno=' + countValue);
	}
</script>

<main>
	<section>
		{#each questionArray as data, index}
			{#if index == countValue - 1}
				<div class="mx-5 mt-5 text-bold ">
					{countValue}. {JSON.parse(data.content_text).question}
				</div>
				{#each JSON.parse(data.content_text).answers as answers, j}
					<div class=" ml-5 mt-1">
						<label>
							<input
								type="radio"
								value={answers.answer}
								name="answer"
								class="ms-5 mt-3"
								id="check"
								radio="radio{j}"
								bind:group={checked__opt[index]}
							/>
							{@html answers.answer}
						</label>
					</div>
				{/each}
			{/if}
		{/each}
	</section>
	<nav class="d-flex mt-1 border-primary bg-light  position-absolute bottom-0 end-0 mr-5">
		<button class="btn m-2">
			{#if minutes == 0 && seconds == 1}
				<div
					class="position-fixed top-50 start-50 d-flex justify-content-center align-items-center w-100 h-100"
					style="transform: translate(-50%, -50%);background-color: rgba(0, 0, 0, 0.3);"
				>
					<div class="p-5 rounded text-center bg-white" style="min-width: 400px;">
						<h5 class="text-dark bg-white">Your time is over</h5>
						<a href="/result"><button class="btn btn-dark">See Your Result</button></a>
					</div>
				</div>
			{:else}
				{#if minutes.toString().length > 1}
					{minutes}:
				{:else}
					0{minutes}: 
				{/if}
				{#if seconds.toString().length > 1}
					{seconds}
				{:else}
					0{seconds}
				{/if}
			{/if}
		</button>
		<Sidebar on:displaySameQues={quesDrop} bind:show={sidebar_show} />
		<button class="btn btn-dark m-2 px-3" on:click={() => (sidebar_show = !sidebar_show)}
			>List</button
		>
		<button
			class="btn btn-dark m-2 px-3"
			on:click={() => switchQuestion('previous')}
			on:click={() => (sidebar_show = false)}
			disabled={countValue <= 1 ? true : false}>Previous</button
		>
		<button class="btn px-2">{countValue} of {questionArray.length}</button>
		<button
			class="btn btn-dark m-2 px-3"
			on:click={() => switchQuestion('next')}
			on:click={() => (sidebar_show = false)}
			disabled={countValue >= questionArray.length ? true : false}>Next</button
		>
		<dialog id="confirmation-dialog" class="height100 border-0 pt-5" style="height:200px;">
			<h3>Are you sure want to end the test?</h3>
			<div class=" mx-auto mt-3" style="width:200px; text-align:center">
				<button class=" btn btn-dark  px-4" on:click={closeClick}>No</button>
				<a href="/result">
					<button class=" btn btn-dark px-4 ">Yes</button>
				</a>
			</div>
		</dialog>

		<button
			class="btn btn-dark m-2 px-3"
			on:click={() => showDialogClick(true)}
			on:click={() => (sidebar_show = false)}>End Test</button
		>
	</nav>
</main>
