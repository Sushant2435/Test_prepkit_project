<script>
	import {answerchoosebyuser, actualcorrect } from '../../store';
	import Sidebar from '../../component/Sidebar.svelte';
	import Navbar from '../../component/Nav.svelte';
	import { onMount } from 'svelte';
	$: sidebar_show = false;
	let questionArray = [];
	let question_no;
	onMount(async () => {
		const res = await fetch('lib/question.json');
		questionArray = await res.json();
		question_no = new URL(location.href).searchParams.get('qno');
		changePageUrl(question_no);
	});
	function quesDrop(event) {
		question_no = event.detail.data;
		changePageUrl(question_no);
	}
	$: question_no = Number(question_no);
    function switchQuestion(event) {
		if (event == 'previous') {
			question_no = question_no - 1;
			changePageUrl(question_no);
		} else {
			question_no = question_no + 1;
			changePageUrl(question_no);
		}
	}
	function changePageUrl(question_no) {
		history.pushState('', '', location.origin + '/explanation?qno=' + question_no);
	}
</script>

<main>
	<Navbar />
	<section>
		{#each questionArray as data, index}
			{#if question_no == index + 1}
				{#if $answerchoosebyuser[index] == $actualcorrect[index]}
					<div
						class="w-fit-content d-table p-md rounded mx-auto p_review_option alert-success  mt-3 px-3 py-1"
					>
						<h5 class="">Correct</h5>
					</div>
				{:else if $answerchoosebyuser[index] == null}
					<div
						class="w-fit-content d-table p-md rounded mx-auto p_review_option alert-warning  mt-3 px-3 py-1"
					>
						<h4 class="">Unattempted</h4>
					</div>
				{:else}
					<div
						class="w-fit-content d-table p-md rounded mx-auto p_review_option alert-danger  mt-3 px-3 py-1"
					>
						<h5 class="">Incorrect</h5>
					</div>
				{/if}
				<div class="mx-5 mt-2 text-bold ">
					{index + 1}. {JSON.parse(data.content_text).question}
				</div>
				{#each JSON.parse(data.content_text).answers as answers}
					{#if answers.is_correct == 1}
						<div class=" ml-5 mt-1">
							<input type="radio" value=" " name="answer" class="ms-5 mt-3" checked /><span
								class="ms-3">{@html answers.answer}</span
							>
						</div>
					{:else}
						<div class=" ml-5 mt-1">
							<input type="radio" value=" " name="answer" class="ms-5 mt-3" disabled /><span
								class="ms-3">{@html answers.answer}</span
							>
						</div>
					{/if}
				{/each}
				<div class="mx-5 mt-5">
					<h4 class="fw-bold">Explanation:</h4>
					{@html JSON.parse(data.content_text).explanation}
				</div>
			{/if}
		{/each}
	</section>

	<nav class="d-flex mt-1 border-primary bg-light  position-absolute bottom-0 end-0 mr-5">
		<Sidebar on:displaySameQues={quesDrop} bind:show={sidebar_show} />
		<button class="btn btn-dark m-2 px-3" on:click={() => (sidebar_show = !sidebar_show)}
			>List</button
		>
		<button
			class="btn btn-dark m-2 px-3"
			on:click={() => switchQuestion('previous')}
			on:click={() => (sidebar_show = false)}
			disabled={question_no <= 1 ? true : false}>Previous</button
		>
		<button class="btn px-2">{question_no} of 11</button>
		<button
			class="btn btn-dark m-2 px-3"
			on:click={() => switchQuestion('next')}
			on:click={() => (sidebar_show = false)}
			disabled={question_no >= 11 ? true : false}
			>Next
		</button>
        <a href="/"><button class="btn btn-dark m-2 px-3">Dashboard</button></a>
		<a href="/result"><button class="btn btn-dark m-2 px-3">Result</button></a>
	</nav>
</main>

<style>
	:global(seq::before) {
		content: attr(no);
		text-transform: uppercase;
	}
</style>
