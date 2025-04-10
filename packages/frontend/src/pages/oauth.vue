<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<PageWithAnimBg>
	<div :class="$style.formContainer">
		<div :class="$style.form">
			<MkAuthConfirm
				ref="authRoot"
				:name="name"
				:icon="logo"
				:permissions="permissions"
				:waitOnDeny="true"
				@accept="onAccept"
				@deny="onDeny"
			/>
		</div>
	</div>
</PageWithAnimBg>
</template>

<script lang="ts" setup>
import * as Misskey from 'cherrypick-js';
import { definePage } from '@/page.js';
import MkAuthConfirm from '@/components/MkAuthConfirm.vue';

const transactionIdMeta = window.document.querySelector<HTMLMetaElement>('meta[name="misskey:oauth:transaction-id"]');
if (transactionIdMeta) {
	transactionIdMeta.remove();
}

const name = window.document.querySelector<HTMLMetaElement>('meta[name="misskey:oauth:client-name"]')?.content;
const logo = window.document.querySelector<HTMLMetaElement>('meta[name="misskey:oauth:client-logo"]')?.content;
const permissions = window.document.querySelector<HTMLMetaElement>('meta[name="misskey:oauth:scope"]')?.content.split(' ').filter((p): p is typeof Misskey.permissions[number] => (Misskey.permissions as readonly string[]).includes(p)) ?? [];

function doPost(token: string, decision: 'accept' | 'deny') {
	const form = window.document.createElement('form');
	form.action = '/oauth/decision';
	form.method = 'post';
	form.acceptCharset = 'utf-8';

	const loginToken = window.document.createElement('input');
	loginToken.type = 'hidden';
	loginToken.name = 'login_token';
	loginToken.value = token;
	form.appendChild(loginToken);

	const transactionId = window.document.createElement('input');
	transactionId.type = 'hidden';
	transactionId.name = 'transaction_id';
	transactionId.value = transactionIdMeta?.content ?? '';
	form.appendChild(transactionId);

	if (decision === 'deny') {
		const cancel = window.document.createElement('input');
		cancel.type = 'hidden';
		cancel.name = 'cancel';
		cancel.value = 'cancel';
		form.appendChild(cancel);
	}

	window.document.body.appendChild(form);
	form.submit();
}

function onAccept(token: string) {
	doPost(token, 'accept');
}

function onDeny(token: string) {
	doPost(token, 'deny');
}

definePage(() => ({
	title: 'OAuth',
	icon: 'ti ti-apps',
}));
</script>

<style lang="scss" module>
.formContainer {
	min-height: 100svh;
	padding: 32px 32px calc(env(safe-area-inset-bottom, 0px) + 32px) 32px;
	box-sizing: border-box;
	display: grid;
	place-content: center;
}

.form {
	position: relative;
	z-index: 10;
	border-radius: var(--MI-radius);
	background-color: var(--MI_THEME-panel);
	box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
	overflow: clip;
	max-width: 500px;
	width: calc(100vw - 64px);
	height: min(65svh, calc(100svh - calc(env(safe-area-inset-bottom, 0px) + 64px)));
	overflow-y: scroll;
}
</style>
