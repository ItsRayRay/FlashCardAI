<script>
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
	flashcard.difficulty = "very hard"
  } else if (event === 6) {
    flashcard.date = new Date();
    flashcard.date.setMinutes(flashcard.date.getMinutes() + 6);
	flashcard.difficulty = "hard"
  } else if (event === 10) {
    flashcard.date = new Date();
    flashcard.date.setMinutes(flashcard.date.getMinutes() + 10);
	flashcard.difficulty = "medium"
  } else if (event === 'max') {
    flashcard.date = new Date();
    flashcard.date.setMinutes(flashcard.date.getMinutes() + 5760);
	flashcard.difficulty = "easy"
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

	flashCardArray = JSON.parse(localStorage.getItem(localStorageKeyFlashCard))

	

   let flashCardArrayss = flashCardArray.sort((a, b) => new Date(a.date) - new Date(b.date));

	console.log(flashCardArrayss)


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
					<div id="buttons" class="btn-group variant-filled">
						<button
							on:click={() => {
								addDelayToCard(1, flashCardArray[0]);
							}}
							class="border-red-500 bg-red-500">Again 1 Min</button
						>
						<button
							on:click={() => {
								addDelayToCard(6, flashCardArray[0]);
							}}
							class="border-red-500 bg-orange-500">Hard 6 Min</button
						>
						<button
							on:click={() => {
								addDelayToCard(10, flashCardArray[0]);
							}}
							class="border-blue-500 bg-yellow-500">Good 10 Min</button
						>
						<button
							on:click={() => {
								addDelayToCard('max', flashCardArray[0]);
							}}
							class="border-red-500 bg-green-500">Easy 4 Days</button
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
						{#if a.difficulty === "easy" }
						<a>
							<span class="badge bg-primary-500">📘</span>
							<span class="flex-auto">{a.question}</span>
							<div class="w-8 h-8 bg-green-500"></div>
							<button type="button" class="btn variant-filled-warning">Delete</button>
						</a>
						{/if}
						{#if a.difficulty === "medium" }
						<a>
							<span class="badge bg-primary-500">📘</span>
							<span class="flex-auto">{a.question}</span>
							<div class="w-8 h-8 bg-yellow-500"></div>
							<button type="button" class="btn variant-filled-warning">Delete</button>
						</a>
						{/if}
						{#if a.difficulty === "hard" }
						<a>
							<span class="badge bg-primary-500">📘</span>
							<span class="flex-auto">{a.question}</span>
							<div class="w-8 h-8 bg-orange-500"></div>
							<button type="button" class="btn variant-filled-warning">Delete</button>
						</a>
						{/if}
						{#if a.difficulty === "very hard" }
						<a>
							<span class="badge bg-primary-500">📘</span>
							<span class="flex-auto">{a.question}</span>
							<div class="w-8 h-8 bg-red-500"></div>
							<button type="button" class="btn variant-filled-warning">Delete</button>
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
		min-width: 30%;
	}

	#cardsection {
		min-width: 40%;
	}

	#saved_cards {
		overflow: auto;
		max-height: 70vh;
		min-width: 30%;
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