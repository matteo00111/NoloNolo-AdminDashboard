<template>
	<div>
		<div>
			<ChartOptionRadioGroup :options="graphDataRangeOptions" :selected="graphDataRangeSelected" @onChange="updateSelected" />
		</div>
		<ChartBase
			:chart-name="name"
			:chart-type="chartType"
			:data="data"
			:data-options="dataOptions"
			:data-labels="dataLabels"
			:aria-label="ariaLabel"
			height="300px"
			@preDraw="$emit('preDraw')"
		/>
	</div>
</template>
<script>
/* eslint-disable no-underscore-dangle */
import helper from '../../assets/helper/helper';
import appearanceConfig from '../../assets/helper/appearanceConfig';

export default {
	props: {
		rentals: [],
		dataOptions: {
			type: Object,
			default: () => ({ backgroundColor: appearanceConfig.backgroundColor }),
		},
		name: {
			type: String,
			required: true,
		},
		chartType: {
			type: String,
			default: 'line',
		},
		ariaLabel: {
			type: String,
			required: true,
		},
	},
	data() {
		return {
			data: [],
			dataLabels: [],
			options: {},
			chartLabel: [],
			graphDataRangeOptions: [
				{ text: '6M', value: '6-M' },
				{ text: '1Y', value: '1-Y', checked: true },
				{ text: '5Y', value: '5-Y' },
			],
			graphDataRangeSelected: '6-M',
		};
	},
	watch: {
		async graphDataRangeSelected() {
			await this.updateGraph();
		},
		async rentals() {
			await this.updateGraph();
		},
	},
	async mounted() {
		await this.updateGraph();
		this.$emit('loaded');
	},
	methods: {
		updateSelected(sel) {
			this.graphDataRangeSelected = sel;
		},

		async updateGraph() {
			const graphPeriod = this.graphDataRangeSelected;
			const dateMap = helper.rentalsToValueDictionary(this.rentals, graphPeriod, (rent) => {
				const endDate = rent.state === 'close' ? new Date(rent.actualEndDate) : new Date(rent.expectedEndDate);
				const value = rent.state === 'close' ? rent.bill.priceRecap.finalPrice : rent.priceEstimation.finalPrice;
				return { endDate, value };
			});

			const data = [];
			const dataLabels = [];
			dateMap.forEach((value, key) => {
				data.push(value);
				dataLabels.push(key);
			});

			this.data = data.length !== 0 ? data : [0];
			this.dataLabels = dataLabels.length !== 0 ? dataLabels : [''];
		},
	},
};
</script>

<style scoped>
#buttonContainer{
	width: min-content;
	margin-left: auto;
	margin-right: 10px;
}

.card-inline-item{
	margin: 15px 10px 15px 10px;
}
</style>
