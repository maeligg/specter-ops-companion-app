<script context="module">
	// since there's no dynamic data here, we can prerender
	// it so that it gets served as a static asset in prod
	export const prerender = true;
</script>

<script>
	import '../app.css';
</script>

<div class="container">
	<h1>About</h1>

	<h2>About Specter Ops</h2>
	<p>
		Specter Ops is a boardgame created by Plaid Hat Games, in which players are trying to locate/capture a mysterious agent, who keeps track of their sneaking via a private map. For more information visit the <a href="https://www.plaidhatgames.com/board-games/specter-ops/">official page</a> on Plaid Hat's website.
	</p>

	<h2>About this app</h2>
	<p>
		This is a fan-made, unofficial web app that is meant to be used by the agent player to track their private information, as an alternative to the paper maps provided in the game box. This is not a fully digital implementation of the game, and you still need the boardgame in order to play. The app should work on all resolutions, although a tablet is probably the most convenient way to use it. Features include :
	</p>
	<ul>
		<li>Randomly generate the objective positions on the map</li>
		<li>Visualise all allowed movements based on your current position</li>
		<li>Keep a log of all your past movements</li>
		<li>Visualise the hunter's line of sights based on their current positions</li>
		<li>Keep track of your health points, completed objectives, used supply caches (if any)</li>
		<li>Can be installed on your device for easier and offline access</li>
	</ul>

	<p>For any questions or suggestions about the app, please <a href="https://github.com/maeligg/specter-ops-companion-app/issues">open an issue</a> on GitHub. The app is and will continue to be 100% ad and tracker free. If you'd like to thank or help me to continue maintaining this app (and other boardgame companion apps), please consider buying me a coffee :</p>

	<a href="https://www.buymeacoffee.com/maelig" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" style="height: 40px !important;width: 145px !important;" ></a>

	<h2>FAQ</h2>
	<details>
		<summary>Can I play with the Artic Archives map ?</summary>
		Not yet, I may add this later if there's any interest so please let me know.
	</details>
	<details>
		<summary>Why is the hunters' car not displayed on the map ?</summary>
		Since the car doesn't directly affect line of sight and the UI is already very busy, I decided not to display it. You can add any hunters in the card directly to the map.
	</details>
	<details>
		<summary>Can I track the usage of my items with the app ?</summary>
		No, you'll need to do it outside the app. If you have ideas / suggestions about how this could be implemented in nice way, please get in touch.
	</details>

	<a href="/game" class="button">Start game</a>
</div>


<style>
	.button {
		margin: 1rem 0;
	}
</style>