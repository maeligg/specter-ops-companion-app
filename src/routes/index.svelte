<script context="module">
	export const prerender = true;
</script>

<script>
	import '../app.css';
</script>

<section>
	<h1>
		<img src="img/title.png" alt="Specter ops" width="370" height="61" />
		<br />
		The unofficial companion app
	</h1>

	<div class="ctas">
		<a href='/game' class='button--primary button--large'>Start</a>
		<a href='/about' class='button button--large'>About</a>
	</div>
</section>

<style>
	section {
		min-height: 100vh;
		margin: auto 0;
		padding: 0 1rem;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 1;
	}

	h1 {
		width: 100%;
	}

	h1 img {
		max-width: 100%;
		margin-bottom: 1rem;
	}

	.ctas {
		width: 100%;
		display: flex;
		justify-content: center;
		gap: 1rem;
	}
</style>
