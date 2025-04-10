<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<MkModalWindow
	ref="dialog"
	:width="400"
	:height="450"
	@close="dialog?.close()"
	@closed="emit('closed')"
>
	<template #header>{{ i18n.ts.renotesList }}</template>

	<MkSpacer :marginMin="20" :marginMax="28">
		<div v-if="renotes" class="_gaps">
			<div v-if="renotes.length === 0" class="_fullinfo">
				<img :src="infoImageUrl" draggable="false"/>
				<div>{{ i18n.ts.nothing }}</div>
			</div>
			<template v-else>
				<MkA v-for="user in users" :key="user.id" :to="userPage(user)" @click="dialog?.close()">
					<MkUserCardMini :user="user" :withChart="false"/>
				</MkA>
			</template>
		</div>
		<div v-else>
			<MkLoading/>
		</div>
	</MkSpacer>
</MkModalWindow>
</template>

<script lang="ts" setup>
import { onMounted, ref, useTemplateRef } from 'vue';
import * as Misskey from 'cherrypick-js';
import MkModalWindow from '@/components/MkModalWindow.vue';
import MkUserCardMini from '@/components/MkUserCardMini.vue';
import { userPage } from '@/filters/user.js';
import { i18n } from '@/i18n.js';
import { infoImageUrl } from '@/instance.js';
import { misskeyApi } from '@/utility/misskey-api.js';

const emit = defineEmits<{
	(ev: 'closed'): void,
}>();

const props = defineProps<{
	noteId: Misskey.entities.Note['id'];
}>();

const dialog = useTemplateRef('dialog');

const renotes = ref();
const users = ref();

onMounted(async () => {
	const res = await misskeyApi('notes/renotes', {
		noteId: props.noteId,
		limit: 30,
	});

	renotes.value = res;
	users.value = res.map(x => x.user);
});
</script>

<style lang="scss" module>
</style>
