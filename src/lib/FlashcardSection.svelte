<script>
	import { compute_rest_props } from 'svelte/internal';

	const currentURL = window.location.href;
	const localStorageKeyFlashCard =
		currentURL.substring(currentURL.lastIndexOf('/') + 1) + 'flashcard';
	let flashCardArray = JSON.parse(localStorage.getItem(localStorageKeyFlashCard)) || [
		{ question: 'please add a flashcard ', anwser: 'hello world' }
	];

	//let randomNumber = Math.floor(Math.random() * flashCardArray.length);

	function addDelayToCard(event, flashcard) {
		if (event === 1) {
			flashcard.date = new Date();
			flashcard.date.setMinutes(flashcard.date.getMinutes() + 1);
			flashcard.difficulty = 'very hard';
		} else if (event === 6) {
			flashcard.date = new Date();
			flashcard.date.setMinutes(flashcard.date.getMinutes() + 6);
			flashcard.difficulty = 'hard';
		} else if (event === 10) {
			flashcard.date = new Date();
			flashcard.date.setMinutes(flashcard.date.getMinutes() + 10);
			flashcard.difficulty = 'medium';
		} else if (event === 'max') {
			flashcard.date = new Date();
			flashcard.date.setMinutes(flashcard.date.getMinutes() + 5760);
			flashcard.difficulty = 'easy';
		}

		let localStorageFlashCard = JSON.parse(localStorage.getItem(localStorageKeyFlashCard));

		if (localStorageFlashCard) {
			const updatedFlashCards = localStorageFlashCard.map((e) => {
				if (e.key === flashcard.key) {
					console.log(flashcard);
					return { ...e, date: flashcard.date, difficulty: flashcard.difficulty };
				}
				return e;
			});

			localStorage.setItem(localStorageKeyFlashCard, JSON.stringify(updatedFlashCards));
			flashCardArray = JSON.parse(localStorage.getItem(localStorageKeyFlashCard));
			flashCardArray = flashCardArray.sort((a, b) => new Date(a.date) - new Date(b.date));
		}
	}

	function toggleCard(e) {
  let getLocalStorage = JSON.parse(localStorage.getItem(localStorageKeyFlashCard));
  console.log(getLocalStorage);

  // Remove the old value if it exists
  getLocalStorage = getLocalStorage.filter((card) => card.key !== e.key);

  // Add the new value at the beginning of the array
  getLocalStorage.unshift(e);

  localStorage.setItem(localStorageKeyFlashCard, JSON.stringify(getLocalStorage));
  flashCardArray = JSON.parse(localStorage.getItem(localStorageKeyFlashCard));
}




	function deleteCard(e) {
		let getLocalStorage = JSON.parse(localStorage.getItem(localStorageKeyFlashCard));

		if (getLocalStorage) {
			const updatedLocalStorage = getLocalStorage.filter((card) => {
				return card.key !== e.key;
			});

			localStorage.setItem(localStorageKeyFlashCard, JSON.stringify(updatedLocalStorage));
			flashCardArray = JSON.parse(localStorage.getItem(localStorageKeyFlashCard));
		}
	}
</script>

<div id="cardcontainer" class="card p-2 flex justify-center">
	<div id="cardsection" class="card p-4">
		<div>
			<div class="flip-card">
				<div class="flip-card-inner">
					<div class="flip-card-front">
						<p class="title">{flashCardArray[0].question}</p>
					</div>
					<div class="flip-card-back">
						<p class="title">{flashCardArray[0].answer}</p>
					</div>
				</div>
			</div>
			<div class="cards p-4 mt-20">
				<div class="flex justify-center">
					<!-- Add this div with classes -->

					<div>
						<button
							on:click={() => {
								addDelayToCard(1, flashCardArray[0]);
							}}
							type="button"
							class="p-3 bg-red-500 hover:bg-red-600">Again 1 Min</button
						>
						<button
							on:click={() => {
								addDelayToCard(6, flashCardArray[0]);
							}}
							type="button"
							class="p-3 bg-orange-500 hover:bg-orange-600">Hard 6 Min</button
						>

						<button
							on:click={() => {
								addDelayToCard(10, flashCardArray[0]);
							}}
							type="button"
							class="p-3 bg-yellow-500 hover:bg-yellow-600">Good 10 Min</button
						>
						<button
							on:click={() => {
								addDelayToCard('max', flashCardArray[0]);
							}}
							type="button"
							class="p-3 bg-green-500 hover:bg-green-600">Easy 4 Days</button
						>
					</div>
				</div>
			</div>
		</div>
	</div>

	<div id="addflascardsection" class="card p-4">
		<h2>add a flashcard</h2>
		<label class="label">
			<span>Input</span>
			<input class="input" type="text" placeholder="Question" />
		</label>

		<label class="label">
			<span>Textarea</span>
			<textarea class="textarea" rows="4" placeholder="Answer." />
		</label>

		<a href="/" class="btn variant-filled">
			<span>Add Card</span>
		</a>
	</div>

	<div id="saved_cards" class="card p-4">
		<nav class="list-nav">
			<h2>Saved Cards</h2>

			<ul>
				<li>
					{#each flashCardArray as a}
						{#if a.difficulty === 'easy'}
							<a>
								<span class="badge bg-primary-500">📘</span>
								<span
									on:click={() => {
										toggleCard(a);
									}}
									class="flex-auto">{a.question}</span
								>
								<div class="w-6 h-6 bg-green-500" />
								<button
									on:click={() => {
										deleteCard(a);
									}}
									type="button"
									class="btn variant-filled-warning">Delete</button
								>
							</a>
						{/if}
						{#if a.difficulty === 'medium'}
							<a>
								<span class="badge bg-primary-500">📘</span>
								<span
									on:click={() => {
										toggleCard(a);
									}}
									class="flex-auto">{a.question}</span
								>
								<div class="w-6 h-6 bg-yellow-500" />
								<button
									on:click={() => {
										deleteCard(a);
									}}
									type="button"
									class="btn variant-filled-warning">Delete</button
								>
							</a>
						{/if}
						{#if a.difficulty === 'hard'}
							<a>
								<span class="badge bg-primary-500">📘</span>
								<span
									on:click={() => {
										toggleCard(a);
									}}
									class="flex-auto">{a.question}</span
								>
								<div class="w-6 h-6 bg-orange-500" />
								<button
									on:click={() => {
										deleteCard(a);
									}}
									type="button"
									class="btn variant-filled-warning">Delete</button
								>
							</a>
						{/if}
						{#if a.difficulty === 'very hard'}
							<a>
								<span class="badge bg-primary-500">📘</span>
								<span
									on:click={() => {
										toggleCard(a);
									}}
									class="flex-auto">{a.question}</span
								>
								<div class="w-6 h-6 bg-red-500" />
								<button
									on:click={() => {
										deleteCard(a);
									}}
									type="button"
									class="btn variant-filled-warning">Delete</button
								>
							</a>
						{/if}
					{/each}
				</li>
			</ul>
		</nav>
	</div>
</div>

<style>
	#addflascardsection {
		min-width: 20%;
	}

	#cardsection {
		min-width: 45%;
	}

	#saved_cards {
		overflow: auto;
		max-height: 70vh;
		min-width: 35%;
	}

	#cardcontainer {
		margin: 0 auto;
		margin-top: 4vh;
		min-height: 70vh;
		max-height: 70vh;
		margin-bottom: 10vh;
		max-width: 90%;
	}

	.flip-card {
		background-color: transparent;
		min-width: 100%;
		max-width: 100%;
		height: 43vh;
		perspective: 1000px;
		font-family: sans-serif;
	}

	.title {
		font-size: 1.5em;
		font-weight: 900;
		text-align: center;
		margin: 0;
	}

	.flip-card-inner {
		position: relative;
		width: 100%;
		height: 100%;
		text-align: center;
		transition: transform 0.8s;
		transform-style: preserve-3d;
	}

	.flip-card:hover .flip-card-inner {
		transform: rotateY(180deg);
	}

	.flip-card-front,
	.flip-card-back {
		box-shadow: 0 8px 14px 0 rgba(0, 0, 0, 0.2);
		position: absolute;
		display: flex;
		flex-direction: column;
		justify-content: center;
		width: 100%;
		height: 100%;
		-webkit-backface-visibility: hidden;
		backface-visibility: hidden;
		border: 1px solid coral;
		border-radius: 1rem;
	}

	.flip-card-front {
		background: linear-gradient(
			120deg,
			rgb(247, 243, 239) 60%,
			rgb(243, 242, 242) 88%,
			rgb(238, 234, 233) 40%,
			rgba(212, 197, 192, 0.603) 48%
		);
		color: coral;
	}

	.flip-card-back {
		background: linear-gradient(
			120deg,
			rgb(230, 223, 220) 30%,
			rgb(219, 214, 212) 88%,
			rgb(230, 227, 224) 40%,
			rgb(221, 211, 207) 78%
		);
		color: rgb(51, 36, 36);
		transform: rotateY(180deg);
	}
</style>
