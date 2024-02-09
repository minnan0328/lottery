<template>
	<div class="lottery-row">
		<div class="">
			<table>
				<thead>
					<tr>
						<template v-for="item in lotteryThead">
							<th>{{item}}</th>
						</template>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(item, idx) in lotteryData">
						<td>{{item.Lotterydate}}</td>
						<td>{{matchLottery(item.Drawnumbersize, idx)}}</td>
					</tr>
				</tbody>
			</table>
		</div>
		<div>
			<label for="">是否開獎過</label>
			<input type="text" v-model="customValue" @input="comparison">
			<p>請輸入六個號碼 範例：1,2,3,4,5,6</p>
			<p>比對結果：</p>
			<template v-if="comparisonResults && customValue">
				<p v-if="comparisonResults">{{comparisonResults.Lotterydate}}</p>
				<p v-if="comparisonResults">{{comparisonResults.Drawnumbersize}}</p>
			</template>
			<span v-else-if="customValue">無結果</span>
		</div>
	</div>
</template>

<script setup lang="ts">
import { ref, reactive, computed, onMounted } from 'vue';
import dataJson from '@/assets/lottery.json';
import type { LotteryData } from '@/types';

const lotteryData = ref<LotteryData[]>(dataJson);
const lotteryThead = reactive(['Lotterydate', 'Drawnumbersize']);
const customValue = ref<string>("");
	const comparisonResults = ref<string | object | null>(null);

const combinations = new Map<string, number>();

const matchLottery = (lottery: string, idx: number) => {
	const pattern: RegExp = /^(?:\d+,){5}\d+/;
	const match: RegExpMatchArray | null = lottery.match(pattern);
	
	const numbersBeforeSixth: string = match ? match[0] :  "";

	combinations.set(numbersBeforeSixth, idx);

	return numbersBeforeSixth;
};

const comparison = (event: Event)  => {
	const target = event.target as HTMLInputElement;
	const index: number | undefined = combinations.get(customValue.value);

	comparisonResults.value = index && combinations.has(customValue.value) ? lotteryData.value[index] : null;

}; 



onMounted(async () => {
	// const numbers: number[] = Array.from({ length: 49 }, (_, i) => i + 1);

	// // // 从 1 到 49 的数字中選取 6 个数字的所有可能组合
	// for (let i = 0; i < numbers.length - 5; i++) {
	// 	for (let j = i + 1; j < numbers.length - 4; j++) {
	// 		for (let k = j + 1; k < numbers.length - 3; k++) {
	// 			for (let l = k + 1; l < numbers.length - 2; l++) {
	// 				for (let m = l + 1; m < numbers.length - 1; m++) {
	// 					for (let n = m + 1; n < numbers.length; n++) {
	// 						const combinationObj = `${numbers[i]}, ${numbers[j]},${ numbers[k]}, ${numbers[l]}, ${numbers[m]}, ${numbers[n]}`;
	// 						combinations.set(combinationObj, 0);
	// 					}
	// 				}
	// 			}
	// 		}
	// 	}
	// }

});

</script>

<style scoped lang="scss">
table{
    border-collapse: collapse;
	width: 100%;
	th, td{
		border: 1px solid #444444;
		padding: 8px 20px;
	}
}

input {
	padding: 8px 16px;
	width: 100%;
}


.lottery-row {
	display: flex;
	gap: 24px 24px;
	width: 100%;
}
</style>
