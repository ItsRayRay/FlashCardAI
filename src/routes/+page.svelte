
<script>
	function handleBackend() {
		async function fetchData() {
  try {
    const response = await fetch('http://127.0.0.1:8000');
    const data = await response.json();
    console.log(data); // {"Ping": "Pong"}
    // You can now use the 'data' object to update your SvelteKit component's state and display the response in your front-end UI
  } catch (error) {
    console.error(error);
  }
}

fetchData();
	}

</script>

<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-5">
		<h1>Welcome FlashCard AI</h1> 
		<h2>Add a subject to get started with our AI chatbot. Upload resources and use our flashcards to learn faster. Start your studying journey today!</h2>
	</div>
</div>
