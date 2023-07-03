<script>
	import { choose_answer, answerchoosebyuser, actualcorrect } from '../store';
	import { onMount } from 'svelte';
	let choosed_answer = $choose_answer;
	var filtered = choosed_answer.filter((elm) => elm);
	let un_attempted = 11 - filtered.length;
	let snippts = [];
	onMount(async () => {
		const res = await fetch('lib/question.json');
		snippts = await res.json();
	});
	let option_box = 4;
	let correct = 0;
	let incorrect = 0;
	let percentage = 0;
	let selectanswerbyuser__arr = [];
	let correctans_arr = [];
	$: for (let i = 0; i < snippts.length; i++) {
		let correct__indx = 0;
		let actualCorrect = 0;
		if ($choose_answer[i]) {
			for (let j = 0; j < option_box; j++) {
				if (JSON.parse(snippts[i].content_text).answers[j].answer == $choose_answer[i]) {
					correct__indx = j;
				}
			}
		} else {
			correct__indx = null;
		}
		for (let j = 0; j < option_box; j++) {
			if (JSON.parse(snippts[i].content_text).answers[j].is_correct == '1') {
				actualCorrect = j;
			}
		}
		correctans_arr[i] = actualCorrect;
		$actualcorrect[i] = actualCorrect;
		selectanswerbyuser__arr[i] = correct__indx;
		$answerchoosebyuser[i] = correct__indx;
	}
	$: for (let i = 0; i < selectanswerbyuser__arr.length; i++) {
		if (selectanswerbyuser__arr[i] != null) {
			if (selectanswerbyuser__arr[i] == correctans_arr[i]) {
				correct = correct + 1;
				percentage = Math.round((correct / 11) * 100);
			} else {
				incorrect = incorrect + 1;
			}
		}
	}
</script>

<main>
	<div class="d-flex mx-auto justify-content-center mt-5">
		<button
			class="btn btn-light custom_btn outline1 filters-action px-5 border-primary me-1 "
			data-filter-value=""
		>
			<div>
				<span class="d-inline-block font18 text-info"> {percentage}%</span>
			</div>
			<span class="d-none d-md-inline-block">Result</span>
		</button>
		<button
			class="btn btn-light custom_btn outline1 filters-action px-4 border-primary  me-1 "
			data-filter-value=""
		>
			<div>
				<span class="d-inline-block font18 text-primary">11</span>
			</div>
			<span class="d-none d-md-inline-block">Items</span>
		</button>
		<button
			class="btn btn-light custom_btn outline1 filters-action px-4 border-primary me-1"
			data-filter-value=""
		>
			<div>
				<span class="d-inline-block font18 text-success"> {correct}</span>
			</div>
			<span class="d-none d-md-inline-block">Correct</span>
		</button>
		<button
			class="btn btn-light custom_btn outline1 filters-action px-4 border-primary  me-1"
			data-filter-value=""
		>
			<div>
				<span class="d-inline-block font18 text-danger"> {incorrect}</span>
			</div>
			<span class="d-none d-md-inline-block">Incorrect</span>
		</button>
		<button
			class="btn btn-light custom_btn outline1 filters-action px-4 border-primary me-1 "
			data-filter-value=""
		>
			<div>
				<span class="d-inline-block font18 text-warning"> {un_attempted}</span>
			</div>
			<span class="d-none d-md-inline-block">Unattempted</span>
		</button>
	</div>
	<table class="table mx-auto" style="width:1350px">
		<thead>
			<tr>
				<th scope="col"><h5>No.</h5></th>
				<th scope="col"><h5>Question</h5></th>
				<th scope="col"><h5>Answer</h5></th>
				<th scope="col"><h5>Status</h5></th>
			</tr>
		</thead>
		<tbody>
			{#each snippts as data, index}
				<tr>
					<th scope="row">{index + 1}.</th>
					<td>
						<a href="explanation?qno={index + 1}" class=" text-decoration-none text-dark "
							>{data.snippet}</a
						>
					</td>

					<td class="d-flex ">
						{#each JSON.parse(data.content_text).answers as _, j}
							<p
								class="{`${
									correctans_arr[index] == j
										? 'bg-success text-white'
										: selectanswerbyuser__arr[index] == j
										? 'bg-danger text-white'
										: ''
								}`}    
									border d-flex justify-content-center align-items-center ms-2  text-dark rounded"
								style="width: 23px; height:23px"
							>
								{String.fromCharCode(65 + j)}
							</p>
						{/each}
					</td>
					<td>
						{#if selectanswerbyuser__arr[index] == correctans_arr[index]}
							<span class="text-success fw-bold">Correct</span>
						{:else if selectanswerbyuser__arr[index] == null}
							<span class="text-warning fw-bold ">Unattempted</span>
						{:else}
							<span class="text-danger fw-bold">Incorrect</span>
						{/if}
					</td>
				</tr>
			{/each}
		</tbody>
	</table>
</main>

<style>
</style>
