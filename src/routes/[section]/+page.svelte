<script lang="ts">
	import { base } from '$app/paths';
	import { page } from '$app/state';
	import { PUBLIC_APP_NAME } from '$env/static/public';
	import type { PageProps } from './$types';

	import { assertCanonicalResourceUri } from '$lib/types/at-uri';
	import { paginate } from '$lib/utils/pagination';

	import PageListing from '$lib/components/page/page-listing.svelte';
	import PostFeedItemSplash from '$lib/components/timeline/post-feed-item-splash.svelte';
	import PostFeedItemNoImage from '$lib/components/timeline/post-feed-item-no-image.svelte';
	import PostFeedItemWithImage from '$lib/components/timeline/post-feed-item-with-image.svelte';

	import FeedMetaTags from '../components/feed-meta-tags.svelte';

	const { data }: PageProps = $props();

	let dedupe = $state(true);

	const rkey = $derived(assertCanonicalResourceUri(data.feed.uri).rkey);

	const regexes = [
		[/\bcanucks\b/, 'sports'],
		[/\bgiants\b/, 'sports'],
		[/\bgoldeneyes\b/, 'sports'],
		[/\bwhitecaps\b/, 'sports'],
		[/\bfifa\b/, 'sports'],
		[/\bnba\b/, 'sports'],
		[/\bnbaer\b/, 'sports'],
		[/\bnhl\b/, 'sports'],
		[/\bpwhl\b/, 'sports'],
		[/\bwhl\b/, 'sports'],
		[/\bsaskatoon mamba\b/, 'sports'],
		[/\bworld cup\b/, 'sports'],
		[/\bbaseball\b/, 'sports'],
		[/\bhockey\b/, 'sports'],
		[/\bfootball\b/, 'sports'],
		[/\bsoccer\b/, 'sports'],
		[/\bswimming\b/, 'sports'],
		[/\bmarathon\b/, 'sports'],
		[/\bolympian\b/, 'sports'],
		[/\bolympics\b/, 'sports'],
		[/\bcycling\b/, 'sports'],
		[/\bballgame\b/, 'sports'],
		[/\bsports\b/, 'sports'],
		[/\bjudge\b/, 'crime'],
		[/\bmounties\b/, 'crime'],
		[/\bmountie\b/, 'crime'],
		[/\bgang\b/, 'crime'],
		[/\bhomicide\b/, 'crime'],
		[/\binvestigators\b/, 'crime'],
		[/\brcmp\b/, 'crime'],
		[/\bvpd\b/, 'crime'],
		[/\bpolice\b/, 'crime'],
		[/\bkilling\b/, 'crime'],
		[/\bsuspicious\b/, 'crime'],
		[/\bcustody\b/, 'crime'],
		[/\bstabbed\b/, 'crime'],
		[/\bcrash\b/, 'crime'],
		[/\bfatal\b/, 'crime'],
		[/\bcrime\b/, 'crime'],
		[/\bgun\b/, 'crime'],
		[/\bstolen\b/, 'crime'],
		[/\bhackers\b/, 'crime'],
		[/\bcriminal\b/, 'crime'],
		[/\bcriminals\b/, 'crime'],
		[/\bcriminalize\b/, 'crime'],
		[/\bcriminalizing\b/, 'crime'],
		[/\bcourt\b/, 'crime'],
		[/\blawyer\b/, 'crime'],
		[/\blawyers\b/, 'crime'],
		[/\bconviction\b/, 'crime'],
		[/\bsentence\b/, 'crime'],
		[/\bparole\b/, 'crime'],
		[/\bbreak-and-enter\b/, 'crime'],
		[/\bsuspect\b/, 'crime'],
		[/\bcops\b/, 'crime'],
		[/\barrested\b/, 'crime'],
		[/\barrest\b/, 'crime'],
		[/\barrests\b/, 'crime'],
		[/\btraffic stop\b/, 'crime'],
		[/\bmurder\b/, 'crime'],
		[/\bassault\b/, 'crime'],
		[/\bfraud\b/, 'crime'],
		[/\brobbery\b/, 'crime'],
		[/\brape\b/, 'crime'],
		[/\briot\b/, 'crime'],
		[/\btheft\b/, 'crime'],
		[/\bsuicide\b/, 'crime'],
		[/\bextortion\b/, 'crime'],
		[/\bmobster\b/, 'crime'],
		[/\bmafia\b/, 'crime'],
		[/\bdiscrimination case\b/, 'crime'],
		[/\bsettlement\b/, 'crime'],
		[/\bliability\b/, 'crime'],
		[/\bliable\b/, 'crime'],
		[/\babuse\b/, 'crime'],
		[/\bpolitics\b/, 'politics'],
		[/\bcouncil\b/, 'politics'],
		[/\bpremier\b/, 'politics'],
		[/\beby\b/, 'politics'],
		[/\bgovernment\b/, 'politics'],
		[/\brob shaw\b/, 'politics'],
		[/\bmayor\b/, 'politics'],
		[/\bmayor's\b/, 'politics'],
		[/\bcity hall\b/, 'politics'],
		[/\bforests minister\b/, 'politics'],
		[/\bconservatives\b/, 'politics'],
		[/\bconservative\b/, 'politics'],
		[/\bndp\b/, 'politics'],
		[/\bliberals\b/, 'politics'],
		[/\bliberal\b/, 'politics'],
		[/\btransit\b/, 'politics'],
		[/\boutlaw\b/, 'politics'],
		[/\boutlaws\b/, 'politics'],
		[/\bnurses\b/, 'health'],
		[/\bnursing\b/, 'health'],
		[/\bdoctor\b/, 'health'],
		[/\bdoctors\b/, 'health'],
		[/\bhospital\b/, 'health'],
		[/\bcancer\b/, 'health'],
		[/\bhiv\b/, 'health'],
		[/\bhealthcare\b/, 'health'],
		[/\bburnaby\b/, 'local'],
		[/\bport moody\b/, 'local'],
		[/\bsurrey\b/, 'local'],
		[/\brichmond\b/, 'local'],
		[/\bdelta\b/, 'local'],
		[/\bnorth vancouver\b/, 'local'],
		[/\bwest vancouver\b/, 'local'],
		[/\bwhite rock\b/, 'local'],
		[/\bladner\b/, 'local'],
		[/\bcoquitlam\b/, 'local'],
		[/\bnew westminster\b/, 'local'],
		[/\bnew west\b/, 'local'],
		[/\blangley\b/, 'local'],
		[/\btsawwassen\b/, 'local'],
		[/\bgastown\b/, 'local'],
		[/\banmore\b/, 'local'],
		[/\bbelcarra\b/, 'local'],
		[/\bgranville island\b/, 'local'],
		[/\bstrathcona\b/, 'local'],
		[/\bwest end\b/, 'local'],
		[/\beast van\b/, 'local'],
		[/\bsteveston\b/, 'local'],
		[/\bdowntown\b/, 'local'],
		[/\bpark\b/, 'local'],
		[/\bbusiness\b/, 'business'],
		[/\bweb summit\b/, 'business'],
		[/\bai\b/, 'business'],
		[/\bretail\b/, 'business'],
		[/\binvests\b/, 'business'],
		[/\binvestment\b/, 'business'],
		[/\bfund\b/, 'business'],
		[/\btrade\b/, 'business'],
		[/\bmarket\b/, 'business'],
		[/\bceo\b/, 'business'],
		[/\bleasing\b/, 'business'],
		[/\bdeveloper\b/, 'business'],
		[/\bdeveloper's\b/, 'business'],
		[/\blng\b/, 'business'],
		[/\bstartups\b/, 'business'],
		[/\bstartup\b/, 'business'],
		[/\bdata centre\b/, 'business'],
		[/\bdata centres\b/, 'business'],
		[/\blumber\b/, 'business'],
		[/\bexports\b/, 'business'],
		[/\btariffs\b/, 'business'],
		[/\bproject\b/, 'business'],
		[/\bcomputing\b/, 'business'],
		[/\bcompanies\b/, 'business'],
		[/\bcompany\b/, 'business'],
		[/\bcorporation\b/, 'business'],
		[/\bprofits\b/, 'business'],
		[/\binflation\b/, 'business'],
		[/\bwholesale\b/, 'business'],
		[/\bwholesales\b/, 'business'],
		[/\bai centres\b/, 'business'],
	];

	let filteredTimeline = $derived({
		...data.timeline,
		items: (() => {
			const seen = new Set();
			return data.timeline.items.filter((item) => {
				let text = <string>item.post.record?.text;
				text = text.replace(/\btheprovince.com\S+\b/, '');
				text = text.replace(/\bvancouversun.com\S+\b/, '');
				if (dedupe && seen.has(text)) {
					return false;
				}
				const embed = <Record<string, object>>item.post.record?.embed;
				const external = <Record<string, string>>embed?.external;
				if (dedupe && seen.has(external?.title)) {
					return false;
				}
				seen.add(text);
				if (external?.title) {
					seen.add(external?.title);
				}

				// Categorize
				let category = 'unknown';
				let titleText: string = embed?.external?.title || text || '';
				titleText = titleText.toLowerCase();
				console.log('Title:', titleText);

				for (const regexPair of regexes) {
					const [re, cat] = regexPair;
					if (titleText.match(re)) {
						category = cat;
						break;
					}
				}
				if (category === 'unknown') {
					let descriptionText: string = embed?.external?.description || '';
					descriptionText = descriptionText.toLowerCase();
					console.log('Description:', descriptionText);
					for (const regexPair of regexes) {
						const [re, cat] = regexPair;
						if (descriptionText.match(re)) {
							category = cat;
							break;
						}
					}
				}
				if (category === 'unknown') {
					console.log('Author:', item.post.author.displayName);
					if (item.post.author.displayName === 'BIV News') {
						category = 'business';
					}
				}
				if (category === 'unknown') {
					console.warn('==>', category);
				} else {
					console.log('==>', category);
				}
				if (page.url.pathname !== `/${category}/`) {
					return false;
				}

				return true;
			});
		})(),
	});

	/*
	(() => {
		console.log('Jim Original Timeline', data.timeline);
		for (const i in filteredTimeline.items) {
			console.log('Jim Item', i, filteredTimeline.items[i].post.record);
		}
	})();
	*/

	const { rootUrl, nextUrl } = $derived(paginate(page.url, filteredTimeline.cursor, base));
</script>

<svelte:head>
	<title>{data.feed.displayName} by @{data.feed.creator.handle} — {PUBLIC_APP_NAME}</title>
	<link rel="canonical" href={base} />
	<link rel="alternate" href={data.feed.uri} />
</svelte:head>

<FeedMetaTags feed={data.feed} />

<PageListing subject="timeline" {rootUrl} {nextUrl}>
	<section class="grid-section">
		<div class="grid1">
			<div class="grid1-1">
				{#if filteredTimeline.items[0]}
					<PostFeedItemSplash item={filteredTimeline.items[0]} />
				{/if}
			</div>
			<div class="grid1-2">
				<div class="subgrid1-2">
					<div class="subgrid1-2-1">
						{#if filteredTimeline.items[1]}
							<PostFeedItemWithImage item={filteredTimeline.items[1]} />
						{/if}
					</div>
					<div class="subgrid1-2-2">
						{#if filteredTimeline.items[2]}
							<PostFeedItemWithImage item={filteredTimeline.items[2]} />
						{/if}
					</div>
					<div class="subgrid1-2-3">
						{#if filteredTimeline.items[3]}
							<PostFeedItemWithImage item={filteredTimeline.items[3]} />
						{/if}
					</div>
				</div>
			</div>
			<div class="grid1-3">
				<div class="subgrid1-3">
					<div class="subgrid1-3-1">
						{#if filteredTimeline.items[4]}
							<PostFeedItemWithImage item={filteredTimeline.items[4]} />
						{/if}
					</div>
					<div class="subgrid1-3-2">
						{#if filteredTimeline.items[5]}
							<PostFeedItemWithImage item={filteredTimeline.items[5]} />
						{/if}
					</div>
					<div class="subgrid1-3-3">
						{#if filteredTimeline.items[6]}
							<PostFeedItemWithImage item={filteredTimeline.items[6]} />
						{/if}
					</div>
					<div class="subgrid1-3-4">
						{#if filteredTimeline.items[7]}
							<PostFeedItemWithImage item={filteredTimeline.items[7]} />
						{/if}
					</div>
				</div>
			</div>
		</div>
	</section>
	<section class="flexbox-section">
		{#each { length: filteredTimeline.items.length - 8 } as _, i}
			<div>
				<PostFeedItemWithImage item={filteredTimeline.items[i + 8]} />
			</div>
		{/each}
	</section>
</PageListing>

<style>
	.grid-section {
	}

	.grid1 {
		display: grid;
		grid-template-rows: min-content minmax(0px, 1fr);
		grid-template-columns: repeat(24, 1fr);
		grid-auto-flow: row;
		gap: 32px 16px;
		margin-inline: 16px;
		width: 100%;
		max-width: calc(1248px);
	}

	@media screen and (max-width: 1279px) {
		.grid1 {
			max-width: calc(100% - 32px);
		}
	}

	@media screen and (max-width: 1007px) {
		.grid1 {
			grid-template-rows: repeat(2, min-content) minmax(0px, 1fr);
		}
	}

	@media screen and (max-width: 767px) {
		.grid1 {
			grid-template-rows: unset;
		}
	}

	/*
	@media screen and (max-width: 599px) {
		.grid1 {
			grid-template-columns: repeat(12, 1fr);
		}
	}
	*/

	.grid1-1 {
		grid-column: 1 / span 18;
	}

	@media screen and (max-width: 767px) {
		.grid1-1 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.grid1-1 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.grid1-1 {
			grid-column: 1 / span 16;
		}
	}

	@media screen and (max-width: 767px) {
		.grid1-1 {
			grid-column: 1 / span 24;
		}
	}

	@media screen and (max-width: 599px) {
		.grid1-1 {
			display: initial;
		}
	}

	/*
	@media screen and (max-width: 599px) {
		.grid1-1 {
			grid-column: 1 / span 12;
		}
	}
	*/

	.grid1-2 {
		grid-area: 1 / 19 / span 2 / span 6;
	}

	@media screen and (max-width: 1007px) {
		.grid1-2 {
			grid-row: 1 / span 3;
		}
	}

	@media screen and (max-width: 1007px) {
		.grid1-2 {
			grid-column: 17 / span 8;
		}
	}

	@media screen and (max-width: 1007px) {
		.grid1-2 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.grid1-2 {
			grid-column: 1 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.grid1-2 {
			display: contents;
		}
	}

	.subgrid1-2 {
		display: grid;
		grid-template-columns: repeat(6, 1fr);
		grid-auto-flow: row;
		gap: 16px;
		width: 100%;
		min-width: fit-content;
		max-width: calc(1248px);
	}

	@media screen and (max-width: 1279px) {
		.subgrid1-2 {
			max-width: min(100% - 32px, 1008px);
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2 {
			grid-template-columns: repeat(8, 1fr);
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2 {
			display: grid;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2 {
			max-width: min(100% - 32px, 722px);
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2 {
			display: contents;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2 {
			max-width: calc(100% - 32px);
		}
	}

	.subgrid1-2-1 {
		grid-column: 1 / span 6;
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2-1 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2-1 {
			grid-column: 1 / span 8;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-1 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-1 {
			grid-row: 2 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-1 {
			grid-column: 1 / span 12;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-2-1 {
			grid-column: 1 / span 24;
		}
	}

	.subgrid1-2-2 {
		grid-column: 1 / span 6;
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2-2 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2-2 {
			grid-column: 1 / span 8;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-2 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-2 {
			grid-row: 2 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-2 {
			grid-column: 13 / span 12;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-2-2 {
			grid-row: 3 / span 1;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-2-2 {
			grid-column: 1 / span 24;
		}
	}

	.subgrid1-2-3 {
		grid-column: 1 / span 6;
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2-3 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-2-3 {
			grid-column: 1 / span 8;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-3 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-3 {
			grid-row: 3 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-2-3 {
			grid-column: 1 / span 12;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-2-3 {
			grid-row: 4 / span 1;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-2-3 {
			grid-column: 1 / span 24;
		}
	}

	.grid1-3 {
		grid-column: 1 / span 18;
	}

	@media screen and (max-width: 1007px) {
		.grid1-3 {
			display: contents;
		}
	}

	@media screen and (max-width: 1007px) {
		.grid1-3 {
			grid-column: 1 / span 1;
		}
	}

	.subgrid1-3 {
		display: grid;
		grid-template-columns: repeat(20, 1fr);
		grid-auto-flow: row;
		column-gap: 16px;
		width: 100%;
		min-width: fit-content;
		max-width: calc(1248px);
	}

	@media screen and (max-width: 1279px) {
		.subgrid1-3 {
			max-width: min(100% - 32px, 1008px);
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3 {
			display: contents;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3 {
			max-width: min(100% - 32px, 1008px);
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3 {
			display: contents;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3 {
			max-width: calc(100% - 32px);
		}
	}

	.subgrid1-3-1 {
		grid-column: 1 / span 5;
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-1 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-1 {
			grid-column: 1 / span 8;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-1 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-1 {
			grid-row: 4 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-1 {
			grid-column: 1 / span 12;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-1 {
			grid-column: 1 / span 24;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-1 {
			grid-row: 5 / span 1;
		}
	}

	.subgrid1-3-2 {
		grid-column: 6 / span 5;
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-2 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-2 {
			grid-column: 9 / span 8;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-2 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-2 {
			grid-row: 4 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-2 {
			grid-column: 13 / span 12;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-2 {
			grid-column: 1 / span 24;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-2 {
			grid-row: 6 / span 1;
		}
	}

	.subgrid1-3-3 {
		grid-column: 11 / span 5;
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-3 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-3 {
			grid-column: 1 / span 8;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-3 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-3 {
			grid-row: 5 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-3 {
			grid-column: 1 / span 12;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-3 {
			grid-column: 1 / span 24;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-3 {
			grid-row: 7 / span 1;
		}
	}

	.subgrid1-3-4 {
		grid-column: 16 / span 5;
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-4 {
			display: initial;
		}
	}

	@media screen and (max-width: 1007px) {
		.subgrid1-3-4 {
			grid-column: 9 / span 8;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-4 {
			display: initial;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-4 {
			grid-row: 5 / span 1;
		}
	}

	@media screen and (max-width: 767px) {
		.subgrid1-3-4 {
			grid-column: 13 / span 12;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-4 {
			grid-column: 1 / span 24;
		}
	}

	@media screen and (max-width: 599px) {
		.subgrid1-3-4 {
			grid-row: 8 / span 1;
		}
	}

	.flexbox-section {
		display: flex;
		flex-wrap: wrap;
		max-width: 1279px;
	}

	.flexbox-section div {
		max-width: 300px;
	}

	@media screen and (max-width: 599px) {
		.flexbox-section div {
			max-width: 599px;
		}
	}
</style>
