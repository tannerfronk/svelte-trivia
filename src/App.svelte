<script>
	import {onMount} from 'svelte'
	let name = ''
	let categories = [];
	let questions = [];

	let selectedCategory = "";
	let difficulty = "";


	const categoryURL = 'https://opentdb.com/api_category.php'
	
	onMount( async function(){
		const res = await fetch(categoryURL);
		let fullData = await res.json();
		categories = fullData.trivia_categories;
		console.log(categories)
	})

	$: questionURL = 'https://opentdb.com/api.php?amount=10' + selectedCategory + difficulty + "&type=multiple";

	async function getQuestions(){
		const res = await fetch(questionURL);
		let fullData = await res.json();
		questions = fullData.results;
		console.log(questions)
	}
	

</script>

<main>
	<div>
	<div id="name">
		<p>Your Name:</p>
		<input bind:value='{name}'>
	</div>
	
	<h1>Hello {name},</h1>
	<h2>Lets Play Some Trivia!</h2>

	<p>Choose a Difficulty</p>
	<button on:click={() => difficulty = ""}>Any</button>
	<button on:click={() => difficulty = "&difficulty=easy"}>Easy</button>
	<button on:click={() => difficulty = "&difficulty=medium"}>Medium</button>
	<button on:click={() => difficulty = "&difficulty=hard"}>Hard</button>

	<p>Select a Category:</p>
	<select bind:value={selectedCategory}>
		<option value="">Any</option>
		{#each categories as category}
			<option value="{"&category" + category.id}">{category.name}</option>
		{/each}
	</select>

	
	<button on:click={getQuestions}>Lets Go!</button>
</div>

	{#each questions as question, i}
		<div class="questionBox">
			<h2>{question.question}</h2>
			<div id="answers">
				<button>{question.correct_answer}</button>
				<button>{question.incorrect_answers[0]}</button>
				<button>{question.incorrect_answers[1]}</button>
				<button>{question.incorrect_answers[2]}</button>
			</div>
		</div>	
	{/each}
	
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 360px;
		margin: 0 auto;
	}
	#name {
		display: flex;
		justify-content: center;
	}
	#name p {
		margin-right: 10px;
	}
	#name input {
		margin-top: 14px;
		height: 26px;
	}
	h1 {
		color: blue;
		font-size: 2em;
		font-weight: 100;
	}
	h2 {
		font-size: 1.5em;
		font-weight: 100;
	}
	.questionBox {
		border: 1px solid black;
		margin-bottom: 1rem;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>