<script setup>
import { ref } from "vue";
import { useDialogStore } from "../../store/dialogStore";
import DialogContainer from "./DialogContainer.vue";

const dialogStore = useDialogStore();
const links = ref(dialogStore.moreInfoContent.links);

function handleClose() {
	// 在关闭对话框时更新 links 的值
	links.value = dialogStore.moreInfoContent.links;
	// 关闭对话框
	dialogStore.dialogs.PreviewOriginalData = false;
}
</script>

<template>
	<DialogContainer :dialog="`PreviewOriginalData`" @on-close="handleClose">
		<div class="PreviewOriginalData">
			<!-- 使用条件语句检查 links 是否存在 -->
			<template v-if="dialogStore.moreInfoContent.links">
				<!-- 如果 links 存在，则显示链接内容 -->
				<a :href="dialogStore.moreInfoContent.links" target="_blank">
					{{ dialogStore.moreInfoContent.links }}
				</a>
			</template>
			<template v-else>
				<!-- 如果 links 不存在，则显示提示信息 -->
				<p>未提供链接</p>
			</template>
		</div>
	</DialogContainer>
</template>

<style scoped lang="scss">
.PreviewOriginalData {
	width: 300px;
	h3 {
		margin-bottom: 0.5rem;
		font-size: var(--font-s);
		font-weight: 400;
		color: var(--color-complement-text);
	}
}
</style>
