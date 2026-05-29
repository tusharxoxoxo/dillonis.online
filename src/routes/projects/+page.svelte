<script lang="ts">
	import { ExternalLinkIcon, GithubIcon, StarIcon } from 'lucide-svelte';
	import { onMount } from 'svelte';

	type Project = {
		name: string;
		description: string;
		language: string;
		stars?: string;
		links: {
			label: string;
			url: string;
		}[];
	};

	let selectedIndex = $state(0);
	let skipScroll = true;

	$effect(() => {
		const index = selectedIndex;
		if (skipScroll) {
			skipScroll = false;
			return;
		}

		requestAnimationFrame(() => {
			if (index === 0) {
				document
					.getElementById('projects-top')
					?.scrollIntoView({ block: 'start' });
				return;
			}

			document
				.getElementById(`project-row-${index}`)
				?.scrollIntoView({ block: 'nearest' });
		});
	});

	const projects: Project[] = [
		{
			name: 'openchaos',
			description:
				'Open-source deterministic simulation and property-based testing — exhaustively explore your system in simulation, inject faults, and get a perfect repro of every failure',
			language: 'Open Source',
			links: [
				{ label: 'GitHub', url: 'https://github.com/tusharhqq/openchaos' }
			]
		},
		{
			name: 'rope-man',
			description:
				"Terminal reimplementation of Armin Ronacher's rope-man game, built with OpenTUI",
			language: 'TypeScript',
			links: [{ label: 'GitHub', url: 'https://github.com/tusharhqq/rope-man' }]
		},
		{
			name: '1millionspheres',
			description: 'PBR Three.js demo with 1,000,000 unique spheres',
			language: 'TypeScript',
			links: [
				{
					label: 'GitHub',
					url: 'https://github.com/tusharhqq/1millionspheres'
				},
				{
					label: 'Demo',
					url: 'https://three-ezinstancedmesh2-dynamic-bvh-cmdalhx5.stackblitz.io'
				}
			]
		},
		{
			name: 'dusty',
			description: 'Market maker simulation with spread and PnL modeling',
			language: 'Rust',
			links: [
				{
					label: 'GitHub',
					url: 'https://github.com/tusharhqq/dusty'
				}
			]
		},
		{
			name: 'automatic-disco',
			description: 'Tree-walking interpreter in Go',
			language: 'Go',
			links: [
				{
					label: 'GitHub',
					url: 'https://github.com/tusharhqq/automatic-disco'
				}
			]
		},
		{
			name: '6666',
			description:
				'High-performance OLTP database in OCaml, inspired by TigerBeetle',
			language: 'OCaml',
			links: [{ label: 'GitHub', url: 'https://github.com/tusharhqq/6666' }]
		}
	];

	onMount(() => {
		function handleKeyDown(event: KeyboardEvent) {
			if (event.key === 'j') {
				event.preventDefault();
				selectedIndex = Math.min(selectedIndex + 1, projects.length - 1);
			} else if (event.key === 'k') {
				event.preventDefault();
				selectedIndex = Math.max(selectedIndex - 1, 0);
			} else if (event.key === 'Enter' || event.key === 'o') {
				event.preventDefault();
				const project = projects[selectedIndex];
				if (!project) return;

				const firstLink = project.links[0];
				if (firstLink?.url) {
					window.open(firstLink.url, '_blank');
				}
			} else if (event.key === 'g' && event.shiftKey) {
				event.preventDefault();
				selectedIndex = projects.length - 1;
			} else if (event.key === 'g') {
				const handler = (e: KeyboardEvent) => {
					if (e.key === 'g') {
						e.preventDefault();
						selectedIndex = 0;
					}
					window.removeEventListener('keydown', handler);
				};
				window.addEventListener('keydown', handler);
				setTimeout(() => window.removeEventListener('keydown', handler), 500);
			}
		}

		window.addEventListener('keydown', handleKeyDown);
		return () => window.removeEventListener('keydown', handleKeyDown);
	});
</script>

<main
	class="mx-auto flex w-full max-w-2xl flex-col px-5 py-10 font-mono text-ctp-text sm:px-8 lg:py-14"
>
	<header id="projects-top" class="mb-10 border-b border-ctp-surface0 pb-8">
		<a
			href="/"
			class="text-sm text-ctp-blue transition-colors hover:text-mode-color"
		>
			← home
		</a>
		<h1 class="mt-5 text-2xl font-semibold tracking-tight text-ctp-text lg:text-3xl">
			Projects
		</h1>
		<p class="mt-3 max-w-prose text-base leading-relaxed text-ctp-subtext1">
			Open source work — simulation, games, demos, and infrastructure.
		</p>
	</header>

	<section aria-label="Open source projects">
		<h2
			class="mb-4 text-xs font-semibold uppercase tracking-[0.2em] text-ctp-overlay0"
		>
			Assorted projects
		</h2>

		<ul class="divide-y divide-ctp-surface0 border-y border-ctp-surface0">
			{#each projects as project, i}
				{@const isSelected = selectedIndex === i}
				<li
					id="project-row-{i}"
					class="group relative transition-colors {isSelected
						? 'bg-ctp-surface0/60'
						: 'hover:bg-ctp-surface0/30'}"
				>
					<div
						role="button"
						tabindex="0"
						class="flex w-full cursor-pointer flex-col gap-2 px-1 py-5 text-left sm:px-2 {isSelected
							? 'border-l-2 border-mode-color pl-3 sm:pl-4'
							: 'border-l-2 border-transparent pl-3 sm:pl-4'}"
						onclick={() => {
							selectedIndex = i;
						}}
						onkeydown={(event) => {
							if (event.key === 'Enter' || event.key === ' ') {
								event.preventDefault();
								selectedIndex = i;
							}
						}}
					>
						<div class="flex flex-wrap items-baseline justify-between gap-x-4 gap-y-1">
							<div class="flex min-w-0 flex-wrap items-baseline gap-x-3 gap-y-1">
								<span class="text-lg font-semibold text-ctp-text">{project.name}</span>
								<span
									class="text-[0.65rem] font-semibold uppercase tracking-wider text-ctp-overlay0"
								>
									{project.language}
								</span>
							</div>
							{#if project.stars}
								<span
									class="flex shrink-0 items-center gap-1 text-sm tabular-nums text-ctp-yellow"
								>
									<StarIcon size={12} />
									{project.stars}
								</span>
							{/if}
						</div>

						<p class="max-w-prose text-sm leading-relaxed text-ctp-subtext1">
							{project.description}
						</p>

						<div class="flex flex-wrap items-center gap-x-3 gap-y-1 text-sm">
							{#each project.links as link, linkIndex}
								<a
									href={link.url}
									target="_blank"
									rel="noopener noreferrer"
									class="inline-flex items-center gap-1 text-ctp-blue transition-colors hover:text-mode-color hover:underline"
									onclick={(event) => event.stopPropagation()}
								>
									{#if link.label === 'GitHub'}
										<GithubIcon size={12} />
									{:else}
										<ExternalLinkIcon size={12} />
									{/if}
									{link.label}
								</a>
								{#if linkIndex < project.links.length - 1}
									<span class="text-ctp-surface2" aria-hidden="true">·</span>
								{/if}
							{/each}
						</div>
					</div>
				</li>
			{/each}
		</ul>
	</section>

	<p class="mt-8 text-xs text-ctp-overlay0">
		j/k navigate · Enter open link · gg top · G bottom
	</p>
</main>
