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

	const projects: Project[] = [
		{
			name: '6666',
			description:
				'A high-performance OLTP database written in OCaml, inspired by TigerBeetle',
			language: 'OCaml',
			links: [{ label: 'GitHub', url: 'https://github.com/westdelhi/6666' }]
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
	class="flex flex-col mx-auto mt-12 text-ctp-text font-mono w-3/4 max-w-4xl"
>
	<!-- Header section -->
	<div class="flex flex-col gap-3 mb-8 items-center">
		<div class="text-ctp-crust bg-mode-color px-3 py-1 text-lg">
			projects.md
		</div>
		<a
			href="/"
			class="text-ctp-blue hover:text-mode-color hover:bg-[#313346] px-2 py-0.5 transition-colors"
		>
			← :bprev
		</a>
	</div>

	<!-- Open Source Projects Section -->
	<div class="flex flex-col gap-4 mb-12">
		<div class="text-ctp-peach font-bold text-lg mb-2">
			" === Open Source Projects ===
		</div>
		<div class="flex flex-col gap-3">
			{#each projects as project, i}
				{@const isSelected = selectedIndex === i}
				<div
					class="group hover:bg-[#313346] transition-colors px-3 py-3 cursor-pointer {isSelected
						? 'bg-[#313346]'
						: ''}"
					onclick={() => {
						selectedIndex = i;
					}}
					role="button"
					tabindex={i}
				>
					<div class="flex flex-row items-start gap-3">
						<span class="text-ctp-overlay0 shrink-0 select-none">{i + 1}.</span>
						<div class="flex flex-col gap-1.5 grow min-w-0">
							<div class="flex flex-row items-baseline gap-2 flex-wrap">
								<span class="text-ctp-text font-semibold">{project.name}</span>
								<span
									class="text-ctp-subtext0 text-sm px-1.5 py-0.5 bg-ctp-surface0 rounded"
									>{project.language}</span
								>
								{#if project.stars}
									<span class="text-ctp-yellow text-sm flex items-center gap-1">
										<StarIcon size={12} />
										{project.stars}
									</span>
								{/if}
							</div>
							<div class="text-sm text-ctp-subtext1 mt-0.5">
								{project.description}
							</div>
							<div class="flex flex-row gap-2 mt-1.5 text-sm flex-wrap">
								{#each project.links as link, linkIndex}
									<a
										href={link.url}
										target="_blank"
										rel="noopener noreferrer"
										class="text-ctp-blue hover:text-mode-color hover:underline flex items-center gap-1"
									>
										{#if link.label === 'GitHub'}
											<GithubIcon size={12} />
										{:else}
											<ExternalLinkIcon size={12} />
										{/if}
										{link.label}
									</a>
									{#if linkIndex < project.links.length - 1}
										<span class="text-ctp-overlay0">|</span>
									{/if}
								{/each}
							</div>
						</div>
					</div>
				</div>
			{/each}
		</div>
	</div>

	<!-- Help text -->
	<div class="text-ctp-overlay0 text-xs mb-12">
		" Navigation: j/k (down/up), gg (top), G (bottom), Enter/o (open first link)
	</div>
</main>
