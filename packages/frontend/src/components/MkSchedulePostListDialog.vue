<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<MkModalWindow
	ref="dialogEl"
	:withOkButton="false"
	@click="cancel()"
	@close="cancel()"
>
	<template #header>{{ i18n.ts.schedulePostList }}</template>
	<MkSpacer :marginMin="20" :marginMax="28">
		<MkPagination ref="paginationEl" :pagination="pagination">
			<template #empty>
				<div class="_fullinfo">
					<img :src="infoImageUrl" draggable="false"/>
					<div>{{ i18n.ts.nothing }}</div>
				</div>
			</template>

			<template #default="{ items }">
				<div class="_gaps">
					<MkNoteSimple v-for="item in items" :key="item.id" :scheduled="true" :note="item.note" @deleteAndEditScheduleNote="listUpdate"/>
				</div>
			</template>
		</MkPagination>
	</MkSpacer>
</MkModalWindow>
</template>

<script lang="ts" setup>
import { ref, useTemplateRef } from 'vue';
import type { Paging } from '@/components/MkPagination.vue';
import MkModalWindow from '@/components/MkModalWindow.vue';
import MkPagination from '@/components/MkPagination.vue';
import MkNoteSimple from '@/components/MkNoteSimple.vue';
import { i18n } from '@/i18n.js';
import { infoImageUrl } from '@/instance.js';

const emit = defineEmits<{
	(ev: 'cancel'): void;
}>();

const dialogEl = useTemplateRef('dialogEl');
const cancel = () => {
	emit('cancel');
	dialogEl.value.close();
};
const paginationEl = ref();
const pagination: Paging = {
	endpoint: 'notes/schedule/list',
	limit: 10,
};

function listUpdate() {
	paginationEl.value.reload();
}
</script>
