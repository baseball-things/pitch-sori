<script lang="ts">
	import type { RealtimePostgresChangesPayload } from '@supabase/supabase-js';
	import { supabase } from './supabaseClient';
	import { onDestroy, onMount } from 'svelte';
	import { browser } from '$app/environment';

	interface Sign {
		id: number;
		created_at: string;
		team_id: string;
		sign_message: string;
	}

	let signs: Sign[] = [];
	let tts;

	onMount(async () => {
		const { data } = await supabase.from('signs').select();
		signs = Array.from(data ?? []);
		if (browser) {
			tts = window.speechSynthesis;
		}
	});

	onDestroy(() => {
		supabase.channel('signs').unsubscribe();
	});

	// Create a function to handle inserts
	const handleInserts = async (payload: RealtimePostgresChangesPayload<Sign>) => {
		console.log('speak');
		if (browser) {
			const msg = new SpeechSynthesisUtterance(payload.new.sign_message ?? '');
			tts.speak(msg);
			const { data } = await supabase.from('signs').select();
			signs = Array.from(data ?? []);
			console.log(payload.new.sign_message);
		}
	};

	// Listen to inserts
	supabase
		.channel('signs')
		.on('postgres_changes', { event: 'INSERT', schema: 'public', table: 'signs' }, handleInserts)
		.subscribe();
</script>

<ul>
	{#each signs as sign}
		<li>{sign.sign_message}</li>
	{/each}
</ul>
