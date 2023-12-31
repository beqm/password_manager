<script lang="ts">
	import '../app.postcss';
	import SearchBar from '$lib/components/SearchBar.svelte';
	import NavItems from '$lib/components/NavItems.svelte';
	import PageWrapper from '$lib/components/PageWrapper.svelte';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import ClientStore from '$lib/stores/ClientStore';
	import { localToStore } from '$lib/utils';
	import { appWindow } from '@tauri-apps/api/window';
	import { invoke } from '@tauri-apps/api';

	let resultQuery: string | undefined = undefined;
	let isCtrlPressed = false;
	let isRPressed = false;

	const logout = () => {
		invoke('logout');
		localStorage.removeItem('client');
		goto('/login');
	};

	appWindow.onCloseRequested(async (event) => {
		let isLocked = await invoke('check_lock');
		if (isLocked) goto('/verify');
	});

	const onKeyDown = (event: KeyboardEvent) => {
		if (event.repeat) return;
		switch (event.key) {
			case 'F5':
				event.preventDefault();
				break;
			case 'Control':
				isCtrlPressed = true;
				break;

			case 'r':
				isRPressed = true;
				break;
		}
		if (isCtrlPressed && isRPressed) {
			event.preventDefault();
		}
	};

	const onKeyUp = (event: KeyboardEvent) => {
		switch (event.key) {
			case 'Control':
				isCtrlPressed = false;
				event.preventDefault();
				break;

			case 'r':
				isRPressed = false;
				event.preventDefault();
				break;
		}
	};

	onMount(async () => {
		await localToStore(ClientStore, 'client', null);
		if ($ClientStore) {
			goto('/verify');
		} else {
			goto('/login');
		}
	});
</script>

<svelte:window on:keydown={onKeyDown} on:keyup={onKeyUp} />

{#if $ClientStore}
	<div class="flex text-dark">
		<nav
			class="h-screen bg-primary-900 w-[25%] max-w-[300px] min-w-[200px] border-r border-primary-800"
		>
			<div class="flex flex-col w-full items-center justify-center h-[20%] xl:h-[15%]">
				<SearchBar bind:resultQuery />
			</div>

			<ul class="h-[80%] xl:h-[85%] flex flex-col">
				<li>
					<NavItems />
				</li>
				<li class="flex mt-auto border-b border-t w-full border-primary-800 p-4">
					<div class="flex justify-start w-[80%] items-center font-bold overflow-hidden text-lg">
						{$ClientStore.username}
					</div>
					<div class="flex justify-center w-[20%] m-2">
						<button class="hover:text-hover mr-4">
							<svg
								fill="currentColor"
								xmlns="http://www.w3.org/2000/svg"
								height="1.2em"
								viewBox="0 0 512 512"
								><!--! Font Awesome Free 6.4.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path
									d="M495.9 166.6c3.2 8.7 .5 18.4-6.4 24.6l-43.3 39.4c1.1 8.3 1.7 16.8 1.7 25.4s-.6 17.1-1.7 25.4l43.3 39.4c6.9 6.2 9.6 15.9 6.4 24.6c-4.4 11.9-9.7 23.3-15.8 34.3l-4.7 8.1c-6.6 11-14 21.4-22.1 31.2c-5.9 7.2-15.7 9.6-24.5 6.8l-55.7-17.7c-13.4 10.3-28.2 18.9-44 25.4l-12.5 57.1c-2 9.1-9 16.3-18.2 17.8c-13.8 2.3-28 3.5-42.5 3.5s-28.7-1.2-42.5-3.5c-9.2-1.5-16.2-8.7-18.2-17.8l-12.5-57.1c-15.8-6.5-30.6-15.1-44-25.4L83.1 425.9c-8.8 2.8-18.6 .3-24.5-6.8c-8.1-9.8-15.5-20.2-22.1-31.2l-4.7-8.1c-6.1-11-11.4-22.4-15.8-34.3c-3.2-8.7-.5-18.4 6.4-24.6l43.3-39.4C64.6 273.1 64 264.6 64 256s.6-17.1 1.7-25.4L22.4 191.2c-6.9-6.2-9.6-15.9-6.4-24.6c4.4-11.9 9.7-23.3 15.8-34.3l4.7-8.1c6.6-11 14-21.4 22.1-31.2c5.9-7.2 15.7-9.6 24.5-6.8l55.7 17.7c13.4-10.3 28.2-18.9 44-25.4l12.5-57.1c2-9.1 9-16.3 18.2-17.8C227.3 1.2 241.5 0 256 0s28.7 1.2 42.5 3.5c9.2 1.5 16.2 8.7 18.2 17.8l12.5 57.1c15.8 6.5 30.6 15.1 44 25.4l55.7-17.7c8.8-2.8 18.6-.3 24.5 6.8c8.1 9.8 15.5 20.2 22.1 31.2l4.7 8.1c6.1 11 11.4 22.4 15.8 34.3zM256 336a80 80 0 1 0 0-160 80 80 0 1 0 0 160z"
								/></svg
							>
						</button>
						<button on:click={logout} class="hover:text-hover">
							<svg
								fill="currentColor"
								xmlns="http://www.w3.org/2000/svg"
								height="1.2em"
								viewBox="0 0 512 512"
								><!--! Font Awesome Free 6.4.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path
									d="M377.9 105.9L500.7 228.7c7.2 7.2 11.3 17.1 11.3 27.3s-4.1 20.1-11.3 27.3L377.9 406.1c-6.4 6.4-15 9.9-24 9.9c-18.7 0-33.9-15.2-33.9-33.9l0-62.1-128 0c-17.7 0-32-14.3-32-32l0-64c0-17.7 14.3-32 32-32l128 0 0-62.1c0-18.7 15.2-33.9 33.9-33.9c9 0 17.6 3.6 24 9.9zM160 96L96 96c-17.7 0-32 14.3-32 32l0 256c0 17.7 14.3 32 32 32l64 0c17.7 0 32 14.3 32 32s-14.3 32-32 32l-64 0c-53 0-96-43-96-96L0 128C0 75 43 32 96 32l64 0c17.7 0 32 14.3 32 32s-14.3 32-32 32z"
								/></svg
							>
						</button>
					</div>
				</li>
			</ul>
		</nav>

		<PageWrapper bind:resultQuery>
			<slot />
		</PageWrapper>
	</div>
{:else}
	<main
		on:contextmenu|preventDefault
		class="text-dark bg-primary-1000 h-screen w-full overflow-hidden"
	>
		<slot />
	</main>
{/if}
