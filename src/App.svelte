<script>
	import {onMount} from 'svelte';
	import _ from 'lodash';
	let name = '';
	let categories = [];
	let questionsPromise = Promise.resolve([]);

	let selectedCategory = "&category9";
	let difficulty = "";
	let correct = 0;
	let wrong = 0;

	const categoryURL = 'https://opentdb.com/api_category.php'
	
	onMount( async function(){
		const res = await fetch(categoryURL);
		let fullData = await res.json();
		categories = fullData.trivia_categories;
	})

	$: questionURL = 'https://opentdb.com/api.php?amount=10' + selectedCategory + difficulty + '&type=multiple';
	
	async function getQuestions(){
		const res = await fetch(questionURL);
		let fullData = await res.json();
		let questionsArray = [];
		let questions = fullData.results;
		questions.forEach(question => {
		let answersArray = [];
			answersArray.push({answer: question.correct_answer, correct: true})
			question.incorrect_answers.forEach(incorrectAnswer => {
				answersArray.push({answer: incorrectAnswer, correct: false})
			})
			answersArray.sort(function (a, b) {return Math.random() - 0.5;});
			questionsArray.push({question: question.question, answers: answersArray})
		});
		return questionsArray
	}

	function handleClick() {
		questionsPromise = getQuestions()
		let btn = document.getElementById('hidden')
		btn.style.zIndex = ''
	}

	function checkAnswer(answer, btnId) {
		let button = document.getElementById(btnId)
		let answerText = document.getElementById(btnId).innerText;
		let chosenAnswer = {answer: answerText, correct: true}
		if(JSON.stringify(answer) === JSON.stringify(chosenAnswer)){
			console.log("correct!");
			button.style.backgroundColor = 'lightgreen';
			correct +=1;
		} else {
			button.style.backgroundColor = 'red';
			wrong +=1;
		}
	}
	
	
</script>

<main>
	<div>
		
	<span id="tracker">
		<p>Correct Answers: {correct}</p>
		<p>Wrong Answers: {wrong}</p>
	</span>

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
		<option value="&category9=">Any</option>
		{#each categories as category}
			<option value="{"&category=" + category.id}">{category.name}</option>
		{/each}
	</select>

</div>


{#await questionsPromise then questionsArray}

	{#each questionsArray as question}
		<div class="questionBox">
			<h2>{@html _.unescape(question.question)}</h2>
			<div id="answers">
				{#each question.answers as answer}
					<button id={answer.answer} on:click={() => checkAnswer(answer, answer.answer)}>{@html _.unescape(answer.answer)}</button>
				{/each}
			</div>
		</div>	
	{/each}
	<button id="hidden" on:click={handleClick}>Questions!</button>
{/await}
	
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 360px;
		margin: 0 auto;
		background-color: #DEF2F1;
	}
	#hidden {
		z-index: 999;
	}
	#tracker {
		position: fixed;
		right: 2%;
		background-color: white;
		padding: 0 10px;
		box-shadow: 1px 1px #FEFFFF;
		border-radius: 5px;
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
		color: #17252A;
		font-size: 2em;
		font-weight: 100;
	}
	h2 {
		font-size: 1.5em;
		font-weight: 100;
	}
	.questionBox {
		margin: 0 auto;
		padding: 1rem;
		margin-top: 1rem;
		margin-bottom: 2rem;
		border-radius: 10px;
		background-color: #3AAFA9;
		color: #DEF2F1;
		max-width: 80%;
	}
	button {
		border-radius: 10px;
		border: none;
		background-color: #FEFFFF;
		padding: 10px;
		margin: 1rem;
	}
	button:active {
		background-color: #4AAFA9;
		color: #FEFFFF;
	}
	button:hover {
		cursor: pointer;
	}
	input {
		border-radius: 10px;
		background-color: #FEFFFF;
	}
	select {
		border-radius: 10px;
		background-color: #FEFFFF;
		border: none;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>