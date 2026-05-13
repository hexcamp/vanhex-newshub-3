<script lang="ts">
	import type { LayoutProps } from './$types';
	import { page } from '$app/state';

	import FeedAside from './components/feed-aside.svelte';

	const { data, children }: LayoutProps = $props();
</script>

{#key data.feed.uri}
	<div class="custom-feed-layout">
		{#if false}
			<div class="aside">
				<FeedAside feed={data.feed} />
			</div>
		{/if}

		<div class="main">
			<div class="header">
				<h1>VanHex News Hub Experiment #3</h1>
				<a href="https://github.com/hexcamp/vanhex-newshub-3">Source on GitHub</a>
				- <a href="https://6kg6rqiaaaaa.vanhex.ca/">Other Experiments</a>
			</div>

			<nav>
				Sections:
				<a href="/" aria-current={page.url.pathname === '/'}>Home</a>
				<a href="/politics" aria-current={page.url.pathname === '/politics/'}>Politics</a>
				<a href="/crime" aria-current={page.url.pathname === '/crime/'}>Crime</a>
				<a href="/sports" aria-current={page.url.pathname === '/sports/'}>Sports</a>
				<a href="/local" aria-current={page.url.pathname === '/local/'}>Local</a>
				<a href="/health" aria-current={page.url.pathname === '/health/'}>Health</a>
			</nav>
			{@render children()}
		</div>
	</div>
{/key}

<style>
	.custom-feed-layout {
		display: grid;
		grid-template-columns: minmax(0, 1fr);
		/* grid-template-areas: 'aside' 'main'; */
		grid-template-areas: 'main';
		justify-content: center;
		gap: 8px;
		/* margin: 24px auto 0; */
		/* max-width: 480px; */

		@media (width >= 640px) {
			/* grid-template-columns: minmax(255px, 320px) minmax(0, 600px); */
			/* grid-template-areas: 'aside main'; */
			/* grid-template-columns: minmax(0, 600px); */
			grid-template-areas: 'main';
			/* max-width: 960px; */
		}

		@media screen and (max-width: 1279px) {
			max-width: calc(100% - 32px);
		}
	}

	.aside {
		display: flex;
		grid-area: aside;
		flex-direction: column;
		gap: 8px;

		@media (width >= 640px) {
			position: sticky;
			top: 0;
			max-height: 100dvh;
			overflow-y: auto;
		}
	}

	.main {
		grid-area: main;
		padding-bottom: 24px;
	}

	.header {
		/* padding-bottom: 24px; */
		padding: 10px;
	}

	.header h1 {
		margin-bottom: 0.5rem;
		line-height: normal;
	}

	nav {
		padding: 10px;
	}

	nav a[aria-current='true'] {
		border-bottom: 2px solid;
	}

	nav a {
		text-decoration: none;
	}
</style>
