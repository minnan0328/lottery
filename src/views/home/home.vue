<template>
	<div class="lottery-row">
		<div>
			<label for="">是否開獎過</label>
			<input type="text" v-model="customValue" @input="comparison">
			<button @click="generateUniqueRandomNumbers">隨機產生</button>
			<p>請輸入六個號碼 範例：6,18,26,39,42,45</p>
			<p>比對結果：</p>
			<template v-if="comparisonResults && customValue">
				<p v-if="comparisonResults">開獎日期：{{comparisonResults.Lotterydate}}</p>
				<p v-if="comparisonResults">開獎號碼：{{matchLottery(comparisonResults.Drawnumbersize)[0]}}</p>
				<p v-if="comparisonResults">特別號：{{matchLottery(comparisonResults.Drawnumbersize)[1]}}</p>
			</template>
			<span v-else-if="customValue">無結果</span>
			<button @click="reset">清除</button>
		</div>
		<div class="lottery-table">
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
						<td :key="displayLottery(item.Drawnumbersize, idx)">{{item.Drawnumbersize}}</td>
					</tr>
				</tbody>
			</table>
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

const matchLottery = (lottery: string) => {
	const pattern: RegExp = /^(?:\d+,){5}\d+/;
	const specialPattern: RegExp = /^(?:\d+,){5}\d+,(.*)$/;
	
	const matches: RegExpMatchArray | null = lottery.match(pattern);
	const special: RegExpMatchArray | null = lottery.match(specialPattern);
	
	if(matches && special) {

		return [matches[0], special[1]];
	} else {
		return [];
	}

};


const displayLottery = (lottery: string, idx: number) => {
	const geyMatch: string[]  = matchLottery(lottery);
	combinations.set(geyMatch[0], idx);
	if(geyMatch) {
		const numbersBeforeSixth: string = geyMatch[0];
	
		return numbersBeforeSixth;
	}
};

const comparison = ()  => {
	const index: number | undefined = combinations.get(customValue.value);
	comparisonResults.value = index != null && combinations.has(customValue.value) ? lotteryData.value[index] : null;
}; 


const reset = () => {
	customValue.value = "";
	comparisonResults.value = null;
}


const generateUniqueRandomNumbers = () =>  {
	const numbers: number[] = [];
	
	while (numbers.length < 6) {
		const randomNumber = Math.floor(Math.random() * 49) + 1;
		if (!numbers.includes(randomNumber)) {
			numbers.push(randomNumber);
		}
	}
	
	numbers.sort((a, b) => a - b);

	customValue.value = numbers.join(',');
	comparison();
}

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
	width: calc(100% - 32px);
}

.lottery-table {
	padding-top: 48px;
}


.lottery-row {
	// display: flex;
	// flex-wrap: wrap;
	// gap: 24px 24px;
	width: 100%;
	// > div {
	// 	width: 100%;
	// }
}
</style>
