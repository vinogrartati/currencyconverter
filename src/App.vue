<template>
	<div class="container mx-auto flex flex-col items-center bg-gray-100 p-4">
		<div v-if="isLoading" class="fixed w-100 h-100 opacity-80 bg-purple-800 inset-0 z-50 flex items-center justify-center">
			<svg class="animate-spin -ml-1 mr-3 h-12 w-12 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
				<circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
				<path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
			</svg>
		</div>
		<div class="container">
			<section>
				<div class="flex">
					<div class="max-w-xs">
						<label for="wallet" class="block text-sm font-medium text-gray-700"
						>Тикер</label
						>
						<div class="mt-1 relative rounded-md shadow-md">
							<input
								v-model="ticker"
								@keydown.enter="addTicker"
								@input="addVariants"
								type="text"
								name="wallet"
								id="wallet"
								class="block w-full pr-10 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-md"
								placeholder="Например RUB"
							/>
						</div>
						<div v-if="0 < variants.length" class="flex bg-white shadow-md p-1 rounded-md shadow-md flex-wrap">
							<span
									@click="ticker = variant"
									v-for="(variant, index) in variants"
									:key="index"
									class="inline-flex items-center px-2 m-1 rounded-md text-xs font-medium bg-gray-300 text-gray-800 cursor-pointer">
								{{ variant}}
							</span>
						</div>
						<div v-if="isTickerExists" class="text-sm text-red-600">Такой тикер уже добавлен</div>
					</div>
				</div>
				<button
					@click="addTicker"
					type="button"
					class="my-4 inline-flex items-center py-2 px-4 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-gray-600 hover:bg-gray-700 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
				>
					<!-- Heroicon name: solid/mail -->
					<svg
						class="-ml-0.5 mr-2 h-6 w-6"
						xmlns="http://www.w3.org/2000/svg"
						width="30"
						height="30"
						viewBox="0 0 24 24"
						fill="#ffffff"
					>
						<path
							d="M13 7h-2v4H7v2h4v4h2v-4h4v-2h-4V7zm-1-5C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8z"
						></path>
					</svg>
					Добавить
				</button>
			</section>

			<template v-if="0 < tickers.length">
				<hr class="w-full border-t border-gray-600 my-4"/>
				<div>
					<button
						class="mb-4 mx-2 inline-flex items-center py-2 px-4 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-gray-600 hover:bg-gray-700 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
						@click="page = page - 1"
						v-if="page > 1"
					>
						Назад
					</button>
					<button
						class="mb-4 mx-2 inline-flex items-center py-2 px-4 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-gray-600 hover:bg-gray-700 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
						@click="page = page + 1"
						v-if="hasNextPage"
					>
						Вперёд
					</button>
					<div>
						Фильтр:
						<input v-model="filter" @input="page = 1">
					</div>
				</div>
				<hr class="w-full border-t border-gray-600 my-4"/>
				<dl class="mt-5 grid grid-cols-1 gap-5 sm:grid-cols-3">
					<div
						v-for="t in paginatedTickers"
						:key="t.name"
						@click="select(t)"
						:class="{
							'border-4': selectedTicker === t
						}"
						class="bg-white overflow-hidden shadow rounded-lg border-purple-800 border-solid cursor-pointer"
					>
						<div class="px-4 py-5 sm:p-6 text-center">
							<dt class="text-sm font-medium text-gray-500 truncate">
								{{ t.name }} - USD
							</dt>
							<dd class="mt-1 text-3xl font-semibold text-gray-900">
								{{ t.price }}
							</dd>
						</div>
						<div class="w-full border-t border-gray-200"></div>
						<button
							@click.stop="handleDelete(t)"
							class="flex items-center justify-center font-medium w-full bg-gray-100 px-4 py-4 sm:px-6 text-md text-gray-500 hover:text-gray-600 hover:bg-gray-200 hover:opacity-20 transition-all focus:outline-none"
						>
							<svg
								class="h-5 w-5"
								xmlns="http://www.w3.org/2000/svg"
								viewBox="0 0 20 20"
								fill="#718096"
								aria-hidden="true"
							>
								<path
									fill-rule="evenodd"
									d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
									clip-rule="evenodd"
								></path>
							</svg>
							Удалить
						</button>
					</div>
				</dl>
				<hr class="w-full border-t border-gray-600 my-4"/>
			</template>
			<section v-if="null !== selectedTicker " class="relative">
				<h3 class="text-lg leading-6 font-medium text-gray-900 my-8">
					{{ selectedTicker.name }} - USD
				</h3>

				<div class="max-w-xs">
					<label for="wallet" class="block text-sm font-medium text-gray-700"
					>Начало периода</label
					>
					<div class="mt-1 relative rounded-md shadow-md my-4">
						<input
								v-model="startDate"
								type="date"
								name="startDate"
								id="startDate"
								class="block w-full pr-10 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-md"
						/>
					</div>
					<label for="wallet" class="block text-sm font-medium text-gray-700"
					>Конец периода</label
					>
					<div class="mt-1 relative rounded-md shadow-md my-4">
						<input
								v-model="endDate"
								type="date"
								name="endDate"
								id="endDate"
								class="block w-full pr-10 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-md"
						/>
					</div>

					<button
							@click="getGraph(selectedTicker.name, startDate, endDate)"
							type="button"
							class="my-4 inline-flex items-center py-2 px-4 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-gray-600 hover:bg-gray-700 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
					>
						Показать
					</button>
				</div>
				<template v-if="0 < graph.length">
					<div class="flex items-end border-gray-600 border-b border-l h-64">
						<div
							v-for="(bar, i) in normalizedGraph"
							:key="i"
							:style="{
								height: `${bar}%`
							}"
							class="bg-purple-800 border"
						>
						</div>
					</div>
				</template>
				<button
					@click="selectedTicker = null"
					type="button"
					class="absolute top-0 right-0"
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						xmlns:xlink="http://www.w3.org/1999/xlink"
						width="30"
						height="30"
						x="0"
						y="0"
						viewBox="0 0 511.76 511.76"
						xml:space="preserve"
						xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://svgjs.com/svgjs ">
					<g>
						<path
							d="M436.896,74.869c-99.84-99.819-262.208-99.819-362.048,0c-99.797,99.819-99.797,262.229,0,362.048    c49.92,49.899,115.477,74.837,181.035,74.837s131.093-24.939,181.013-74.837C536.715,337.099,536.715,174.688,436.896,74.869z     M361.461,331.317c8.341,8.341,8.341,21.824,0,30.165c-4.16,4.16-9.621,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    l-75.413-75.435l-75.392,75.413c-4.181,4.16-9.643,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    c-8.341-8.341-8.341-21.845,0-30.165l75.392-75.413l-75.413-75.413c-8.341-8.341-8.341-21.845,0-30.165    c8.32-8.341,21.824-8.341,30.165,0l75.413,75.413l75.413-75.413c8.341-8.341,21.824-8.341,30.165,0    c8.341,8.32,8.341,21.824,0,30.165l-75.413,75.413L361.461,331.317z"
							fill="#718096"
							data-original="#000000"
						></path>
					</g>
				</svg>
				</button>
			</section>
		</div>
	</div>
</template>

<script>

export default {
	name: 'App',
	data() {
		return {
			ticker:         '',
			tickers:        [],
			selectedTicker: null,
			startDate:      null,
			endDate:        null,
			graph:          [],
			isLoading:      true,
			symbols:        null,
			variants:       [],
			isTickerExists: false,
			page:           1,
			filter:         '',
		};
	},
	created() {
		const windowData = Object.fromEntries(new URL(window.location).searchParams.entries());

		const VALID_KEYS = ['filter', 'page'];
		VALID_KEYS.forEach(key => {
			if (windowData[key]) {
				this[key] = windowData[key];
			}
		});

		const tickersData = localStorage.getItem('currency-list');

		if (tickersData) {
			this.tickers = JSON.parse(tickersData);
			this.tickers.forEach(t => {
				if ('-' === t.price) {
					this.getPrice(t.name);
				}
			})
		}
	},
	mounted() {
		setTimeout(() => this.isLoading = false, 100);

		this.getSymbols();
	},

	computed: {
		start() {
			return (this.page - 1) * 6;
		},

		end() {
			return this.page * 6;
		},

		filteredTickers() {
			return this.tickers.filter(t => t.name.includes(this.filter.toUpperCase()));
		},

		paginatedTickers() {
			return this.filteredTickers.slice(this.start, this.end);
		},

		hasNextPage() {
			return this.filteredTickers.length > this.end
		},

		normalizedGraph() {
			const maxValue = Math.max(...this.graph);
			const minValue = Math.min(...this.graph);
			return this.graph.map((price) => 5 + ((price - minValue) * 95) / (maxValue - minValue));
		},

		pageStateOptions() {
			return {
				filter: this.filter,
				page:   this.page,
			}
		}
	},
	methods: {
		addTicker() {
			const newTicker = {
				name:  this.ticker.toUpperCase(),
				price: '-'
			};

			this.ticker = '';
			this.variants = [];
			this.isTickerExists = !!this.tickers.find(t => t.name === newTicker.name);
			if (this.isTickerExists) {
				return;
			}

			this.tickers = [...this.tickers, newTicker];


			this.getPrice(newTicker.name);
		},

		handleDelete(ticker) {
			this.tickers = this.tickers.filter(t => ticker !== t);
			if (ticker === this.selectedTicker) {
				this.selectedTicker = null;
			}
		},

		select(ticker) {
			this.selectedTicker = ticker;
		},

		addVariants() {
			const tickerLetters = this.ticker.toUpperCase().split('');
			const result = this.symbols.filter((symbol) => {
				let condition = true;
				tickerLetters.forEach((s, i) => {
					if (s !== symbol[i]) {
						condition = false;
					}
				});
				return condition;
			});
			this.variants = 3 < result.length ? result.slice(0, 4) : result;
		},

		async getSymbols() {
			const f = await fetch(`https://api.apilayer.com/exchangerates_data/symbols`, {
				headers: {
					'apikey': 'C9QUaFBz65U7y2lYRhn4NdaIvQhCv6BB',
				}
			});

			const data = await f.json();
			this.symbols = Object.keys(data.symbols);
		},

		async getPrice(name) {
			const f = await fetch(`https://api.apilayer.com/exchangerates_data/latest?base=USD&symbols=${name}`, {
				headers: {
					'apikey': 'C9QUaFBz65U7y2lYRhn4NdaIvQhCv6BB',
				}
			});

			const data = await f.json();
			if (data.rates) {
				const price = Object.values(data.rates)[0];
				this.tickers.find(t => t.name === name).price = price > 1 ? price.toFixed(2) : price.toPrecision(2);
			}
		},

		async getGraph(name, start, end) {
			this.graph = [];
			const f = await fetch(`https://api.apilayer.com/exchangerates_data/timeseries?start_date=${start}&end_date=${end}&base=USD&symbols=${name}`, {
				headers: {
					'apikey': 'C9QUaFBz65U7y2lYRhn4NdaIvQhCv6BB',
				}
			});
			const data = await f.json();
			Object.values(data.rates).forEach((i) => {
				this.graph.push(...Object.values(i));
			}
			);

		}
	},

	watch: {
		selectedTicker() {
			this.startDate = null;
			this.endDate   = null;
			this.graph     = [];
		},

		tickers() {
			localStorage.setItem('currency-list', JSON.stringify(this.tickers));
		},

		paginatedTickers() {
			if (0 === this.paginatedTickers.length && 1 < this.page) {
				this.page -= 1;
			}
		},

		filter() {
			this.page = 1;
		},
		pageStateOptions(v) {
			window.history.pushState(
				null,
				document.title,
				`${window.location.pathname}?filter=${v.filter}&page=${v.page}`
			);
		},

	}
}
</script>
