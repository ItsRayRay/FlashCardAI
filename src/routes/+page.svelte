<script>
	import { onMount } from "svelte";
	import { setContext } from 'svelte';

	let selectedLanguageToString;
	
	onMount(() => {
		selectedLanguageToString = JSON.stringify(localStorage.getItem("selectedLanguage"));
	});

	let welcomeText = {
		header: "Welcome FlashCard AI",
		body: "Add a subject to get started with our AI chatbot. Upload resources and use our flashcards to learn faster. Start your studying journey today!"
	};

	$: if(selectedLanguageToString){
		switch (selectedLanguageToString) {
			case JSON.stringify('english'):
			  welcomeText = {
				header: "Welcome to FlashCard AI",
				body: "Add a subject to start using our AI chatbot. Upload resources and use our flashcards to learn faster. Start your learning journey today!"
			  };
			  break;
			case JSON.stringify('spanish'):
			  welcomeText = {
				header: "Bienvenido a FlashCard AI",
				body: "Agrega una materia para comenzar a usar nuestro chatbot de inteligencia artificial. Sube recursos y usa nuestras tarjetas para aprender más rápido. ¡Comienza tu camino de estudio hoy mismo!"
			  };
			  break;
			case JSON.stringify('portuguese'):
			  welcomeText = {
				header: "Bem-vindo ao FlashCard AI",
				body: "Adicione uma disciplina para começar a usar nosso chatbot de IA. Faça upload de recursos e use nossos flashcards para aprender mais rápido. Comece sua jornada de estudos hoje mesmo!"
			  };
			  break;
			case JSON.stringify('french'):
			  welcomeText = {
				header: "Bienvenue à FlashCard AI",
				body: "Ajoutez une matière pour commencer à utiliser notre chatbot d'IA. Téléchargez des ressources et utilisez nos fiches pour apprendre plus vite. Commencez votre parcours d'étude dès aujourd'hui !"
			  };
			  break;
			case JSON.stringify('dutch'):
			  welcomeText = {
				header: "Welkom bij FlashCard AI",
				body: "Voeg een onderwerp toe om te beginnen met onze AI-chatbot. Upload bronnen en gebruik onze flashcards om sneller te leren. Begin vandaag nog aan je leertraject!"
			  };
			  break;
			case JSON.stringify('german'):
			  welcomeText = {
				header: "Willkommen bei FlashCard AI",
				body: "Fügen Sie ein Thema hinzu, um mit unserem AI-Chatbot zu beginnen. Laden Sie Ressourcen hoch und verwenden Sie unsere Karteikarten, um schneller zu lernen. Starten Sie noch heute Ihre Lernreise!"
			  };
			  break;
			default:
		}
	}
</script>

<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-5">
		<h1>{welcomeText.header}</h1> 
		<h2>{welcomeText.body}</h2>
	</div>
</div>