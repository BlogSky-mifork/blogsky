<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<PageWithAnimBg>
	<div :class="$style.formContainer">
		<form :class="$style.form" class="_panel" @submit.prevent="submit()">
			<div :class="$style.banner">
				<i class="ti ti-user-check"></i>
			</div>
			<div class="_gaps_m" style="padding: 32px;">
				<div>{{ i18n.tsx.clickToFinishEmailVerification({ ok: i18n.ts.gotIt }) }}</div>
				<div>
					<MkButton gradate large rounded type="submit" :disabled="submitting" data-cy-admin-ok style="margin: 0 auto;">
						{{ submitting ? i18n.ts.processing : i18n.ts.gotIt }}<MkEllipsis v-if="submitting"/>
					</MkButton>
				</div>
			</div>
		</form>
	</div>
</PageWithAnimBg>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import MkButton from '@/components/MkButton.vue';
import { i18n } from '@/i18n.js';
import * as os from '@/os.js';
import { misskeyApi } from '@/utility/misskey-api.js';
import { login } from '@/accounts.js';

const submitting = ref(false);

const props = defineProps<{
	code: string;
}>();

function submit() {
	if (submitting.value) return;
	submitting.value = true;

	misskeyApi('signup-pending', {
		code: props.code,
	}).then(res => {
		return login(res.i, '/');
	}).catch(() => {
		submitting.value = false;

		os.alert({
			type: 'error',
			title: i18n.ts.somethingHappened,
			text: i18n.ts.signupPendingError,
		});
	});
}
</script>

<style lang="scss" module>
.formContainer {
	min-height: 100svh;
	padding: 32px 32px 64px 32px;
	box-sizing: border-box;
	display: grid;
	place-content: center;
}

.form {
	position: relative;
	z-index: 10;
	border-radius: var(--MI-radius);
	box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
	overflow: clip;
	max-width: 500px;
}

.banner {
	padding: 16px;
	text-align: center;
	font-size: 26px;
	background-color: var(--MI_THEME-accentedBg);
	color: var(--MI_THEME-accent);
}
</style>
