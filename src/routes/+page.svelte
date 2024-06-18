<script lang="ts">
	import { clsx } from 'clsx';

	let description = '';

	function toMarkdown(text: string): string {
		return text.replaceAll(/^-\s*(?<label>.+):\s*(?<link>https?.*)$/gm, (...args) => {
			const groups = args.at(-1);

			return `- [${groups.label}](${groups.link})`;
		});
	}

	$: markdown = toMarkdown(description);

	let copyButtonState: 'idle' | 'success' | 'error' = 'idle';
	let copyButtonStateTimerId: number | undefined;

	async function copyMarkdown() {
		copyButtonState = 'idle';
		clearTimeout(copyButtonStateTimerId);

		try {
			await navigator.clipboard.writeText(markdown);

			copyButtonState = 'success';
		} catch (err) {
			console.error(err);

			copyButtonState = 'error';
		} finally {
			copyButtonStateTimerId = setTimeout(() => {
				copyButtonState = 'idle';
			}, 1_000);
		}
	}
</script>

<div class="mx-auto max-w-xl px-2">
	<header class="flex justify-center py-8">
		<h1 class="text-center text-4xl font-semibold">YouTube Description to Markdown</h1>
	</header>

	<div class="prose mt-6">
		<p>
			I publish my podcast episodes on Transistor and YouTube as videos. I have to write the
			description of my episodes on both tools.
		</p>

		<p>
			Transistor supports Markdown, but YouTube doesn't. That's usually fine, except for links. I
			write them in a specific format on YouTube which doesn't match Markdown's format.
		</p>

		<p>On YouTube, I write links like:</p>

		<code>- The label of the link: https://google.com</code>

		<p>While it should be that with Markdown:</p>

		<code>- [The label of the link](https://google.com)</code>

		<p>
			So, I built this little tool to transform both formats! <b
				>No data is sent to a server; everything operates locally on your browser.</b
			>
		</p>
	</div>

	<div class="mt-16 space-y-6">
		<div>
			<label for="description" class="block text-sm font-medium leading-6 text-gray-900">
				YouTube description
			</label>

			<div class="mt-1">
				<textarea
					bind:value={description}
					placeholder="Paste your current YouTube description"
					rows="4"
					id="description"
					class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
				></textarea>
			</div>
		</div>

		<div class="flex justify-center">
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="1.5"
				stroke="currentColor"
				class="size-8 text-gray-400"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M19.5 13.5 12 21m0 0-7.5-7.5M12 21V3"
				/>
			</svg>
		</div>

		<div class="">
			<div class="flex items-center justify-between">
				<label for="markdown" class="block text-sm font-medium leading-6 text-gray-900">
					Markdown
				</label>

				<button
					type="button"
					class={clsx(
						'rounded px-2 py-1 text-xs font-semibold text-white shadow-sm focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2',
						{
							'bg-indigo-600 hover:bg-indigo-500 focus-visible:outline-indigo-600':
								copyButtonState === 'idle',
							'bg-green-600 hover:bg-green-500 focus-visible:outline-green-600':
								copyButtonState === 'success',
							'bg-red-600 hover:bg-red-500 focus-visible:outline-red-600':
								copyButtonState === 'error'
						}
					)}
					on:click={copyMarkdown}
				>
					{#if copyButtonState === 'idle'}
						Copy
					{:else if copyButtonState === 'success'}
						Copied
					{:else}
						Error
					{/if}
				</button>
			</div>

			<div class="mt-1">
				<textarea
					value={markdown}
					placeholder="The generated Markdown will be shown here"
					readonly
					rows="4"
					id="markdown"
					class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
				></textarea>
			</div>
		</div>
	</div>

	<footer class="prose py-16 text-center">
		Made by <a href="https://baptiste.devessier.fr" target="_blank" rel="external"
			>Baptiste Devessier</a
		>
	</footer>
</div>
