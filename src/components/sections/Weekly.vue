<template>
	<sec id="weekly">
		<section-title>Weekly Planner</section-title>
		<div class="weekly">
			<div v-for="day in days" :key="day" class="day">
				<element-title bold>{{ day }}</element-title>
				<div
					v-for="mealtime in mealtimes"
					:key="mealtime"
					class="mealtime"
					:class="mealtime.toLowerCase()"
				>
					<drop
						class="selected-meals"
						@drop="
							(data, native) => handleDrop({ day, mealtime }, data, native)
						"
						@dragenter="enter"
						@dragleave="leave"
					>
						<span
							v-for="main in meal(day, mealtime).filter(m => m.type === 'Main')"
							:key="main.title"
							class="badge badge-main"
						>
							{{ main.title }}
							<button
								type="button"
								:aria-label="`Remove ${main.title}`"
								@click="removeMealFromDay({ day, mealtime, meal: main })"
							>
								<svg stroke="currentColor" fill="none" viewBox="0 0 8 8">
									<path
										stroke-linecap="round"
										stroke-width="1.5"
										d="M1 1l6 6m0-6L1 7"
									/>
								</svg>
							</button>
						</span>
						<span
							v-for="side in meal(day, mealtime).filter(m => m.type === 'Side')"
							:key="side.title"
							class="badge badge-side"
						>
							{{ side.title }}
							<button
								type="button"
								:aria-label="`Remove ${side.title}`"
								@click="removeMealFromDay({ day, mealtime, meal: side })"
							>
								<svg stroke="currentColor" fill="none" viewBox="0 0 8 8">
									<path
										stroke-linecap="round"
										stroke-width="1.5"
										d="M1 1l6 6m0-6L1 7"
									/>
								</svg>
							</button>
						</span>
					</drop>
					<span class="label" v-text="mealtime" />
				</div>
			</div>
		</div>
	</sec>
</template>

<script>
	import { mapGetters, mapActions } from 'vuex'

	import Sec from '../layout/Sec.vue'
	import SectionTitle from '../titles/SectionTitle.vue'
	import ElementTitle from '../titles/ElementTitle.vue'
	import Drop from '../elements/Drop.vue'

	export default {
		name: 'Weekly',
		components: { ElementTitle, Sec, SectionTitle, Drop },
		data() {
			return {
				days: [
					'Sunday',
					'Monday',
					'Tuesday',
					'Wednesday',
					'Thursday',
					'Friday',
					'Saturday'
				],
				mealtimes: ['Breakfast', 'Lunch', 'Dinner']
			}
		},
		methods: {
			handleDrop({ day, mealtime }, data, native) {
				if (native?.target.classList) {
					this.addMealToDay({ day, mealtime, meal: data })
					native.target.classList.remove('bg-blue-400')
					native.stopImmediatePropagation()
				}
			},
			enter(data, native) {
				if (native?.target.classList) {
					native.target.classList.add('bg-blue-400')
					native.stopImmediatePropagation()
				}
			},
			leave(data, native) {
				if (native?.target.classList) {
					native.target.classList.remove('bg-blue-400')
					native.stopImmediatePropagation()
				}
			},
			...mapActions(['addMealToDay', 'removeMealFromDay'])
		},
		computed: {
			...mapGetters(['meal'])
		}
	}
</script>

<style scoped>
	.weekly {
		@apply grid grid-flow-col grid-cols-7 border-2 border-gray-600 rounded;

		grid-template-rows: repeat(4, auto);

		.day {
			@apply contents;

			.element-title {
				@apply bg-blue-200 py-1 text-center;
			}

			div {
				min-height: 8rem;
			}

			.mealtime {
				@apply flex flex-col flex-no-wrap bg-white;

				.selected-meals {
					@apply flex-grow p-3;

					.badge {
						@apply inline-flex items-center m-0.5 py-0.5 rounded-full font-medium;

						&-main {
							@apply px-3 text-sm leading-5 bg-green-100 text-green-800;

							button {
								@apply text-green-500;

								&:focus {
									@apply text-green-700;
								}
							}
						}

						&-side {
							@apply px-2.5 text-xs leading-4 bg-teal-100 text-teal-800;

							button {
								@apply text-teal-500;

								&:focus {
									@apply text-teal-700;
								}
							}
						}

						button {
							@apply flex-shrink-0 ml-1.5 inline-flex;

							&:focus {
								@apply outline-none;
							}

							svg {
								@apply h-2 w-2;
							}
						}
					}
				}

				.label {
					@apply text-center text-gray-500 text-sm py-1;
				}
			}

			&:nth-child(odd) .mealtime:nth-child(even) {
				@apply bg-blue-50;
			}

			&:nth-child(even) .mealtime:nth-child(odd) {
				@apply bg-blue-50;
			}
		}
	}
</style>