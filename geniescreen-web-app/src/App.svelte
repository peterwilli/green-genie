<script>
	import BackgroundRemovalView from './views/BackgroundRemovalView.svelte'
	import Header from './views/Header.svelte'
	import ChooseBar from './views/ChooseBar.svelte'
	let state = 0;
</script>

<main>
	<Header></Header>
	<ChooseBar bind:state="{state}"></ChooseBar>

	{#if state == 1}
	<div class="center">
		<BackgroundRemovalView></BackgroundRemovalView>
	</div>
	{/if}
</main>

<style>
.center {
	text-align: center;
}
</style>