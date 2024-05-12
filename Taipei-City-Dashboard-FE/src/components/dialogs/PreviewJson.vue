<script setup>
import { ref, computed } from "vue";
import { useDialogStore } from "../../store/dialogStore";
import DialogContainer from "./DialogContainer.vue";
const dialogStore = useDialogStore();
const fileType = ref("JSON");
const name = ref(dialogStore.moreInfoContent.name);
function handleClose() {
	name.value = dialogStore.moreInfoContent.name;
	dialogStore.dialogs.previewJson = false;
}
const parsedJson = computed(() => {
	let json = {};
	json.data = dialogStore.moreInfoContent.chart_data;
	if (dialogStore.moreInfoContent.chart_config.categories) {
		json.categories = dialogStore.moreInfoContent.chart_config.categories;
	}

	const jsonString = JSON.stringify(json, null, 2);
	// const base64Json = btoa(jsonString)
	return jsonString;
});
</script>

<template>
	<!-- dialog: 彈跳視窗的名稱 -->
	<!-- @on-close: 處理彈跳視窗關閉的函式 -->
	<DialogContainer :dialog="`previewJson`" @on-close="handleClose">
		<div class="previewjson" v-if="name && fileType === 'JSON'">
			<h2>預覽json</h2>
			<div class="container-dialog">{{ parsedJson }}</div>
		</div>
	</DialogContainer>
</template>
<style scoped lang="scss">
.previewjson {
	width: 350px;
	height: 400px;
	overflow: hidden;
	h2 {
		margin-bottom: 0.5rem;
		margin-top: 1rem;
		color: white;
		text-align: center;
	}
	h2:before {
		content: "-----";
		width: 100;
	}
	h2:after {
		content: "-----";
		width: 100;
	}
}
.container-dialog {
	font-size: var(--font-s);
	width: 320;
	height: 160px;
	resize: none;
	text-align: center;
}
</style>
