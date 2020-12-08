<script>
	import Fa from 'svelte-fa';
	import { faFrown, faCheck } from '@fortawesome/free-solid-svg-icons';

	export let follows;
</script>

<div>
	<div id="result" class={follows ? 'yes' : 'no'}>
		<Fa icon={follows ? faCheck : faFrown} color={follows ? '#49ea24' : '#ea242f'}/>
		{follows ? 'YES' : 'NO'}
	</div>
	<small><a href="">github</a> {follows ? 'follows' : 'does not follow' } <a href="">molnarmark</a></small>
</div>

<style>
	#result {
		margin-top: 50px;
		width: 100%;
		font-size: 60px;
		text-align: center;
	}

	#result.yes {
		color: #49ea24;
	}

	#result.no {
		color: #ea242f;
	}
</style>
