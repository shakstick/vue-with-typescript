<script setup lang="ts">
import { DateTime } from 'luxon';
import { ref, computed } from 'vue';
import { TimeLinePost, today, thisWeek, thisMonth } from '../posts';
import TimelineItem from './TimelineItem.vue';

// type Period = 'Today' | 'This Week' | 'This Month';
const periods = ['Today', 'This Week', 'This Month'] as const;

type Period = (typeof periods)[number];

const selectedPeriod = ref<Period>('Today');
function selectPeriod(period: Period) {
	selectedPeriod.value = period;
}

const posts = computed<TimeLinePost[]>(() => {
	return [today, thisWeek, thisMonth]
		.map((post) => {
			return {
				...post,
				created: DateTime.fromISO(post.created),
			};
		})
		.filter((post) => {
			if (selectedPeriod.value === 'Today') {
				return post.created >= DateTime.now().minus({ day: 1 });
			}

			if (selectedPeriod.value === 'This Week') {
				return post.created >= DateTime.now().minus({ week: 1 });
			}

			return post;
		});
});
</script>

<template>
	<nav class="is-primary panel">
		{{ selectedPeriod }}
		<span class="panel-tabs">
			<a
				v-for="(period, index) in periods"
				:key="index"
				@click="selectPeriod(period)"
				:class="{ 'is-active': selectedPeriod === period }"
			>
				{{ period }}
			</a>
		</span>

		<a>
			<TimelineItem v-for="post in posts" :key="post.id" :post="post" />
		</a>
	</nav>
</template>

<style scoped></style>
