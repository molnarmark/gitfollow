<script>
	import { onMount } from 'svelte'
	import Fa from 'svelte-fa';
	import { faEye, faQuestion } from '@fortawesome/free-solid-svg-icons';
	import ConfettiGenerator from "confetti-js";

	import fetch from 'node-fetch';

	import Result from './components/Result.svelte';
	import BuiltBy from './components/BuiltBy.svelte';

	const githubUsers = [
		"sindresorhus",
		"kentcdodds",
		"wesbos",
		"gvanrossum",
		"abramov",
		"Rich-harris",
		"szmarczak",
		"ry",
		"egoist"
	].sort(() => Math.random() - 0.5);

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

	let user, target;

	let doesFollow = null;
	// Check if user follows the another user
	async function check() {
		if (!user || !target) return;
		const endpoint = `https://api.github.com/users/${user}/following/${target}`;

		const response = await fetch(endpoint, {
			headers: {
				'Accept': 'application/vnd.github.v3+json'
			}
		});

		doesFollow = response.status === 204 ? true : false;

		if (doesFollow) {
			confetti.render();
			initConfetti();
		}
	}

	function reset() {
		doesFollow = null;
	}

	function handleEnter(event) {
		if (event.code === 'Enter') {
			check();
		}
	}
</script>

<div on:keyup={handleEnter}>
	<div id="waves"></div>
	<canvas id="confetti"></canvas>
	<div id="container">
		<div id="logo"><span class="green">Git</span>Follow</div>
		<span class="tag-line">does..</span>
		<div id="inputs">
			<div class="input-element">
				<input spellcheck="false" type="text" name="1" placeholder={placeholder} on:focus={onFocus} on:blur={onBlur} bind:value={user} on:input={reset}>
			</div>
			<div class="follows-icon">
				<span><Fa color="#169192" icon={faEye} size="2x"/></span>
				FOLLOW
			</div>
			<div class="input-element">
				<input spellcheck="false" type="text" name="1" placeholder="molnarmark" bind:value={target} on:input={reset}>
			</div>
			<div class="question-icon"><Fa icon={faQuestion} color="#169192" size="2x"/></div>
			<button id="check-button" on:click={check}>check</button>
		</div>
		{#if doesFollow !== null}
		<Result follows={doesFollow} user={user} target={target}/>
		{/if}
		<BuiltBy />
	</div>
</div>

<style>
.tag-line {
	font-size: 40px;
	margin-bottom: 20px;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}

#waves {
	width: 100vw;
	height: 100vh;
	background: url('pattern.jpg');
	background-repeat: repeat;
	background-position: left top;
	pointer-events: none;
	position: absolute;
	opacity: 0.4;
	z-index: 1;
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
	position: relative;
	z-index: 2;
}

#container #check-button {
	margin-top: 20px;
	height: 40px;
	border: none;
	outline: none;
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
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;}

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

#logo {
	font-size: 80px;
	font-family: 'Montserrat', sans-serif;
	font-weight: 600;
	margin-bottom: 100px;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;}

#logo .green {
	color: #109a48;
	font-weight: 900;
}

@media (max-width: 1024px) {
	#inputs {
		flex-direction: column;
	}

	.follows-icon, .question-icon {
		margin: 10px 0;
	}
}

@media (max-width: 420px) {
	.follows-icon, .question-icon {
		margin: 10px 0;
	}

	#logo {
		font-size: 60px;
		margin-bottom: 40px;
	}
}

@media (max-width: 320px) {
	.follows-icon, .question-icon {
		margin: 10px 0;
	}

	#logo {
		font-size: 50px;
		margin-bottom: 30px;
	}
}
</style>
