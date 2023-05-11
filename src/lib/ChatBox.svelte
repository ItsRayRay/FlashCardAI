<script lang="ts">
	import { page } from '$app/stores';
	import { FileDropzone, localStorageStore } from '@skeletonlabs/skeleton';
	import { Modal, modalStore } from '@skeletonlabs/skeleton';
	import type { ModalSettings, ModalComponent } from '@skeletonlabs/skeleton';


	// Initialize necessary variables
	let lastMessageFromChat = []; // Array to store the last message sent by the user
	let message = ''; // String to store user's message
	let chatContent = [{ name: 'AIBOT', message: 'hello world' }]; // Array to store chat content with initial message from AI
	let scrollToDiv: HTMLDivElement; // Element to scroll to when new message is added

	// Function to handle clicking the send button
	async function handleClick() {
		// Send a POST request to the Langchain API with the last message sent by the user and the subjectId
		console.log('awaiting response');

		const response = await fetch('/api/Langchain', {
			method: 'POST',
			body: JSON.stringify({ message: lastMessageFromChat[0], source: $page.params.subjectId })
		});

		// If response is ok, add the response to the chat content
		if (response.ok) {
			if (chatContent[chatContent.length - 1].name === 'user') {
				chatIsLoading = false
				const data = await response.json();
				chatContent.push(data);
				// Save the chat content to localStorage for this subjectId
				const getLocalStorageAPI = localStorage.getItem(subjectId);
				const getlocalStorageAPIToArray = JSON.parse(getLocalStorageAPI || '[]');
				getlocalStorageAPIToArray.push(data);
				const localStorageAPItoString = JSON.stringify(getlocalStorageAPIToArray);
				localStorage.setItem(subjectId, localStorageAPItoString);
				chatContent = [...chatContent];
				// Scroll to the bottom of the chat box
				scrollToBotton();
			}
		}
	}



	// Function to scroll to the bottom of the chat box
	function scrollToBotton() {
		setTimeout(function () {
			scrollToDiv.scrollIntoView({
				behavior: 'smooth',
				block: 'end',
				inline: 'nearest'
			});
		}, 100);
	}

	// Get subjectId from $page.params.subjectId
	let subjectId = '';
	// Using a reactive statement to set the subjectId when $page.params.subjectId changes
	$: {
		subjectId = $page.params.subjectId;
	}

	// Function to send user's message
	function sendMessage() {
		const messageObj = {
			name: 'user',
			message: message
		};

		// Check if the last message in chatContent is from AI, if so, add the user's message to chatContent
		if (chatContent[chatContent.length - 1].name === 'AIBOT') {
			const getLocalChat = localStorage.getItem(subjectId);
			const getlocalChatToArray = JSON.parse(getLocalChat || '[]');
			getlocalChatToArray.push(messageObj);
			const setArraytoString = JSON.stringify(getlocalChatToArray);
			localStorage.setItem(subjectId, setArraytoString);
			message = '';
			chatContent = [...chatContent];
		}
	}

	// Handle key press event
	function handleKeyPress(event) {
		if (event.key === 'Enter') {
			sendMessage();
			getLastChatMessage();
			lastMessageFromChat.unshift(getLastChatMessage().message);
			handleClick();
			scrollToBotton();
		}
	}

	const alert: ModalSettings = {
		type: 'alert',
		// Data
		title: 'Flashcard has been added',
		body: 'Flashcard has been added'
	};
	// Modal settings

	// Handle modal submit
	function handleModalSubmit(e) {
		// Trigger the modal with the 'confirm' settings and the passed-in event 'e'
		modalStore.trigger(alert);

		// Find the chatContent event that matches the passed-in event 'e'
		const matchingEvent = chatContent.find((event) => event.message === e);
		if (!matchingEvent) return;

		// Create a new flash card object from the matching event and the following event
		const flashCard = {
			question: matchingEvent.message,
			answer: chatContent[chatContent.indexOf(matchingEvent) + 1]?.message,
			key: chatContent.indexOf(matchingEvent),
			difficulty: 'very hard',
			date: new Date()
		};

		// Check if the flash card already exists in the stored list of flash cards
		const existingFlashCards = JSON.parse(localStorage.getItem(subjectId + 'flashcard') || '[]');
		if (existingFlashCards.some((card) => card.key === flashCard.key)) {
			return; // do not add the new flash card to the stored list
		}

		// Add the new flash card to the stored list of flash cards
		const updatedFlashCards = [...existingFlashCards, flashCard];
		localStorage.setItem(subjectId + 'flashcard', JSON.stringify(updatedFlashCards));
	}
	// Get chat content from local storage and update chatContent array
	$: {
		const chatConentfromLocal = localStorage.getItem(subjectId);
		const chatConentfromLocaltoArray = JSON.parse(chatConentfromLocal || '[]');
		chatContent = chatConentfromLocaltoArray;
		console.log(chatContent);
	}

	function getLastChatMessage() {
		const items = localStorage.getItem(subjectId);
		const itemsstringified = JSON.parse(items);
		const lastItem = itemsstringified[itemsstringified.length - 1];

		if (lastItem.name === 'user') {
			return lastItem;
		}
	}

	function runBothFunctions() {
		sendMessage();
		getLastChatMessage();
		lastMessageFromChat.unshift(getLastChatMessage().message);
		handleClick();
		scrollToBotton();
		chatIsLoading = true
	}
</script>


<div id="cardcontainer" class="card p-4">
	<div id="chatcontainer" class="card p-4" style="overflow-y: scroll;">
		<div class="flex items-start mb-4">
			<div id="chatbubble" class="flex flex-col">
				{#each chatContent as msg}
					{#if msg.name === 'AIBOT'}
						<div class="flex items-start mb-4">
							<div class="flex-shrink-0 mr-2">
								<img
									class="h-10 w-10 rounded-full"
									src="https://cdn.icon-icons.com/icons2/1371/PNG/512/robot02_90810.png"
									alt="Profile Picture"
								/>
							</div>
							<div class="bg-gray-200 dark:bg-gray-700 rounded-lg py-2 px-4 shadow-md">
								<p class="text-sm font-medium text-gray-900 dark:text-gray-100">StudyGenie</p>
					
								<p class="text-sm text-gray-700 dark:text-gray-300">{msg.message}</p>
							</div>
						</div>
					{:else if msg.name === 'user'}
						<div class="flex items-start mb-4">
							<div class="flex-grow" />
							<div class="bg-gray-200 dark:bg-gray-700 rounded-lg py-2 px-4 shadow-md text-right">
								<p class="text-sm font-medium text-gray-900 dark:text-gray-100">You</p>
								<p class="text-sm text-gray-700 dark:text-gray-300">{msg.message}</p>
								{#if chatContent[chatContent.length - 1].name === 'AIBOT'}
									<button
										on:click={() => {
											handleModalSubmit(msg.message);
										}}
										type="button"
										class="btn-icon variant-filled ml-2 mt-2">💾</button
									>
								{/if}
							</div>
							<div class="flex-shrink-0 ml-2">
								<img
									class="h-10 w-10 rounded-full"
									src="https://media.istockphoto.com/vectors/default-profile-picture-avatar-photo-placeholder-vector-illustration-vector-id1223671392?k=6&m=1223671392&s=612x612&w=0&h=NGxdexflb9EyQchqjQP0m6wYucJBYLfu46KCLNMHZYM="
									alt="Profile Picture"
								/>
							</div>
						</div>
					{/if}
				{/each}
				<div class="" bind:this={scrollToDiv} />
			</div>
		</div>
	</div>

	<div class="flex flex-row">
		<textarea
			class="textarea flex-item"
			rows="4"
			placeholder="Chat"
			bind:value={message}
			on:keypress={handleKeyPress}
		/>
		<button type="button" class="btn btn-xl variant-filled flex-item" on:click={runBothFunctions}
			>Send</button
		>
	</div>
	<div class="mt-5">
		<FileDropzone name="files" />
	</div>
	<div>
		<dl class="list-dl">
			<div>
				<span class="badge bg-primary-500">📼</span>
				<span class="flex-auto">
					<dt>Title</dt>
					<dd>Description</dd>
				</span>
				<span class="badge bg-primary-500">📖</span>
				<span class="flex-auto">
					<dt>Title</dt>
					<dd>Description</dd>
				</span>
				<span class="badge bg-primary-500">📖</span>
				<span class="flex-auto">
					<dt>Title</dt>
					<dd>Description</dd>
				</span>
			</div>
		</dl>
	</div>
	<div />
</div>

<style>
	#cardcontainer {
		max-width: 90%;
		margin: 0 auto;
		margin-bottom: 1em;
	}

	#chatcontainer {
		max-width: 99%;
		margin: 0 auto;
		min-height: 43vh;
		max-height: 43vh;
		margin-bottom: 1.8em;
		overflow-y: scroll;
		scroll-behavior: smooth;
		scroll-margin-top: 9999px;
	}

	#chatbubble {
		min-width: 100%;
	}

	h1 {
		margin: 0 auto;
	}
</style>
