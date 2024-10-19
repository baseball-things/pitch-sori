<script>
	import SignMessage from '$lib/SignMessage.svelte';
	import { supabase } from '$lib/supabaseClient';

	let team_id;
	let sign_message;

	const handleSubmit = async () => {
		const { error } = await supabase
			.from('signs')
			.insert({ team_id: team_id, sign_message: sign_message });
	};
</script>

<h1>피치소리</h1>
<form on:submit|preventDefault={handleSubmit}>
	<input type="text" placeholder="team_id" bind:value={team_id} />
	<input type="text" placeholder="sign_message" bind:value={sign_message} />
	<button type="submit">go</button>
</form>

<SignMessage />
