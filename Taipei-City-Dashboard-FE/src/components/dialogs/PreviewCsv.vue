<script setup>
import { ref, computed } from "vue";
import { useDialogStore } from "../../store/dialogStore";
import DialogContainer from "./DialogContainer.vue";
import { jsonToCsv } from "../../assets/utilityFunctions/jsonToCsv";
const dialogStore = useDialogStore();
const fileType = ref("JSON");
const name = ref(dialogStore.moreInfoContent.name);
function handleClose() {
	name.value = dialogStore.moreInfoContent.name;
	dialogStore.dialogs.previewCsv = false;
}

const parsedJson = computed(() => {
	let json = {};
	json.data = dialogStore.moreInfoContent.chart_data;
	if (dialogStore.moreInfoContent.chart_config.categories) {
		json.categories = dialogStore.moreInfoContent.chart_config.categories;
	}

	const jsonString = encodeURIComponent(JSON.stringify(json));
	// const base64Json = btoa(jsonString)
	return jsonString;
});
const parsedCsv = computed(() => {
	const chartData = dialogStore.moreInfoContent.chart_data;
	const csvString = chartData
		? jsonToCsv(chartData, dialogStore.moreInfoContent.chart_config)
		: "";
	return chartData;
});
const isDataValid_percentagechart = (data) => {
	return (
		Array.isArray(data) &&
		data.every((item) => {
			return item.hasOwnProperty("name") && item.hasOwnProperty("data");
		})
	);
};
function hasTimestamp(jsonData) {
	for (let series of jsonData.data) {
		for (let point of series.data) {
			const timestamp = new Date(point.x).getTime();
			if (isNaN(timestamp)) {
				return false; // 如果時間戳記無效，返回 false
			}
		}
	}
	return true; // 所有時間戳記都有效，返回 true
}
</script>

<template>
	<!-- dialog: 彈跳視窗的名稱 -->
	<!-- @on-close: 處理彈跳視窗關閉的函式 -->
	<DialogContainer :dialog="`previewCsv`" @on-close="handleClose">
		<div class="previewcsv">
			<h2>CSV預覽</h2>

			<div
				class="dialouge-container"
				v-if="isDataValid_percentagechart(parsedCsv)"
			>
				<div v-for="(item, index) in parsedCsv" :key="index">
					<div class="title" style="flex">{{ item.name }}</div>

					<DIV class="data">{{ item.data }}</DIV>

					<!--{{ item.value }}-->
					<div>
						<div
							v-for="(dataItem, index) in item.data"
							:key="index"
						>
							<div
								v-if="
									dataItem.hasOwnProperty('x') &&
									dataItem.hasOwnProperty('y')
								"
							>
								<p>{{ dataItem.x }}, {{ dataItem.y }}</p>
							</div>
						</div>
					</div>
				</div>
			</div>

			<div v-else class="dialouge-container">
				<div
					v-for="(series, seriesIndex) in parsedCsv"
					:key="seriesIndex"
				>
					<div
						v-for="(point, pointIndex) in series.data"
						:key="pointIndex"
					>
						{{ point.x }},
						{{ point.y }}
						<br />
					</div>
				</div>
			</div>
		</div>
	</DialogContainer>
</template>
<style scoped lang="scss">
.previewcsv {
	width: 620px;
	height: 400px;
	text-align: center;
	overflow: scroll;
	h2 {
		margin-bottom: 0.5rem;
		margin-top: 1rem;
		color: white;
		text-align: center;
	}
	h2:before {
		content: "------";
		width: 100;
	}
	h2:after {
		content: "------";
		width: 100;
	}
}
.dialouge-container {
	text-align: center;
	margin: 20, 10, 10, 20;
}
.data {
	text-align: center;
	margin-top: 20;
	margin-bottom: 20px;
}
.title {
	margin-top: 20px;
	margin-bottom: 20px;
}
</style>
