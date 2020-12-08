<script>
	import { onMount } from 'svelte'
	import Fa from 'svelte-fa';
	import { faEye, faFrown, faCheck, faQuestion } from '@fortawesome/free-solid-svg-icons';
	import Result from './components/Result.svelte';
	import ConfettiGenerator from "confetti-js";

	const githubUsers = [
		"sindresorhus",
		"kentcdodds",
		"wesbos",
		"szekelymilan",
		"Rich_harris",
		"szmarczak",
		"ry",
		"egoist"
	];

	let confetti;
	function initConfetti() {
		const confettiElement = document.getElementById('confetti');
		const confettiSettings = { target: confettiElement, respawn: false, size: 2, max: 150, clock: 120, start_from_edge: true};
		confetti = new ConfettiGenerator(confettiSettings);
	}

	onMount(() => {
		initConfetti();
	})

	const placeholderSwitcher = {
		max: githubUsers.length,
		current: 0
	}

	let placeholder = githubUsers[placeholderSwitcher.current];

	let focused = false;
	const onFocus =()=> {
		focused = true;
		placeholder = '';
	};
	const onBlur =()=> focused = false;

	function switchExamples() {
		if (focused) return;

		placeholderSwitcher.current++;
		if (placeholderSwitcher.current >= placeholderSwitcher.max) {
			placeholderSwitcher.current = 0;
		}

		placeholder = githubUsers[placeholderSwitcher.current];
	}

	setInterval(switchExamples, 2000);
	// Check if user follows the another user
	function check() {
		confetti.render();
		initConfetti();
	}
</script>

<div id="body">
	<canvas id="confetti"></canvas>
	<div id="container">
			<span class="tag-line">does..</span>
		<div id="inputs">
			<div class="input-element">
				<input type="text" name="1" placeholder={placeholder} on:focus={onFocus} on:blur={onBlur}>
			</div>
			<div class="follows-icon">
				<span><Fa color="#169192" icon={faEye} size="2x"/></span>
				FOLLOW
			</div>
			<div class="input-element">
				<input type="text" name="1" placeholder="molnarmark">
			</div>
			<Fa icon={faQuestion} color="#169192" size="2x"/>
			<button id="check-button" on:click={check}>check</button>
		</div>

		<Result follows={true}/>

		<div class="built-by">
			Built with 💙 by Mark Molnar
		</div>
	</div>
</div>

<style>
	.tag-line {
		font-size: 40px;
		margin-bottom: 20px;
	}

	#confetti {
		position: absolute;
		width: 100vw;
		height: 100vh;
		pointer-events: none;
	}

	#container {
		width: 70%;
		height: 100vh;
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	#container #check-button {
		margin-top: 20px;
		height: 40px;
		border: none;
		background: #169192;
		width: 95%;
		font-size: 20px;
		border-radius: 5px;
		transition: all 200ms ease-in-out;
		font-weight: bold;
	}

	#container #check-button:hover {
		cursor: pointer;
		background: #106D6E;
	}

	#inputs {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		flex-wrap: wrap;
	}

	.follows-icon {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	.input-element {
		height: 70px;
		width: 320px;
		position: relative;
		display: flex;
		align-items: center;
		margin: 0 20px;
	}

	.input-element input {
		height: 70px;
		width: 320px;
		border: none;
		background: none;
		text-align: center;
		font-size: 24px;
		border-radius: 5px;
		background: rgba(0, 0, 0, 0.2);
	}

	.input-element input:focus {
		outline: none;
		border-bottom: solid 2px #169192;
		border-radius: 0px;
	}

	.input-element input::placeholder {
		color: rgba(255, 255, 255, 0.3);
		transition: all 500ms ease-in-out;
	}

	.built-by {
		position: absolute;
		display: flex;
		justify-content: center;
		align-items: center;
		bottom: 0px;
		width: 100vw;
		height: 40px;
		color: white;
		font-weight: normal;
	}

</style>
