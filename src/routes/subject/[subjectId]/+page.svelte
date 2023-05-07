<script lang="ts">
	import { page } from '$app/stores';
	import { FileDropzone } from '@skeletonlabs/skeleton';
	import { Modal, modalStore } from '@skeletonlabs/skeleton';
	import type { ModalSettings, ModalComponent } from '@skeletonlabs/skeleton';
	import ChatBox from '../../../lib/ChatBox.svelte';
	import FlashcardSection from '../../../lib/FlashcardSection.svelte';
	import { TabGroup, Tab } from '@skeletonlabs/skeleton';

	let tabSet: number = 0;


	$: subjectId = $page.params.subjectId;



</script>

<div class="container">
<h2 class="mx-auto" id="theSubject">{subjectId}</h2>

<TabGroup>
	<Tab bind:group={tabSet} name="tab1" value={0}>Chat</Tab>
	<Tab bind:group={tabSet} name="tab2" value={1}>Flashcards</Tab>
	<!-- Tab Panels --->
	<svelte:fragment slot="panel">
		{#if tabSet === 0}
			<ChatBox  />
		{:else if tabSet === 1}
			<FlashcardSection />
		{/if}
	</svelte:fragment>
</TabGroup>



</div>

<style>
	h1 {
		margin-top: 0.1em;
		margin-bottom: 0.1em;
	}

	.container {
		margin: 0 auto;
		min-width: 100%;
	}

</style>