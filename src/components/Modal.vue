<template>
	<div class="modal" @mousedown.prevent="">
		<div class="modal-wrapper">
			<div class="modal-content">
				<div class="days-wrapper">
					<div class="days">
						<div v-for="day in daysCount" :key="day" class="day-item">
							{{ weekDays[day + firstDay - 1] }}
						</div>
					</div>
				</div>
				<div class="string" v-for="(line, index) in formatedCells" :key="index">
					<div>номер {{ firstLineNumber + index }}</div>
					<div v-for="(cell, index) in line" :key="index" class="cell">
						<input
							v-model="matrixCopy[cell.position.line][cell.position.index]"
							type="checkbox"
						/>
					</div>
				</div>
				<div class="buttons">
					<button @click="close">Отмена</button>
					<button @click="accept">Применить</button>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: 'Modal',
	props: {
		data: Array,
		matrix: Array,
		weekDays: Array,
	},
	data() {
		return {
			cells: [...this.data],
			matrixCopy: [...this.matrix.map(arr => [...arr])]
		}
	},
	computed: {
		firstLineNumber() {
			return parseInt(this.cells[0].position.line) + 1
		},
		linesCount() {
			return parseInt(this.cells[this.cells.length - 1].position.line) - parseInt(this.cells[0].position.line) + 1
		},
		firstDay() {
			return parseInt(this.cells[0].position.index)
		},
		daysCount() {
			return Math.max(...this.cells.map(cell => cell.position.index)) + 1 - this.firstDay
		},
		formatedCells() {
			const result_arr = []
			const lineLength = this.cells.length / this.linesCount
			for (let i = 0; i < this.linesCount; ++i) {
				const tmp_arr = []
				for (let j = i * lineLength; j < (i + 1) * lineLength; ++j) {
					tmp_arr.push(this.cells[j])
				}
				result_arr.push(tmp_arr)
			}
			return result_arr
		},
	},
	methods: {
		accept() {
			this.$emit('edit', this.matrixCopy)
			this.close()
		},
		close () {
			this.$emit('close')
		}
	}
}
</script>

<style lang="scss" scoped>
.modal {
	position: fixed;
	display: grid;
	justify-content: center;
	background-color: rgba(0, 50, 50, 0.5);
	top: 0;
	left: 0;
	bottom: 0;
	right: 0;
	.modal-content {
		padding: 20px;
		.days-wrapper {
			display: flex;
			.days {
				display: flex;
				margin-left: auto;
				align-items: center;
				justify-content: center;
				div {
					width: 60px;
				}
			}
		}
		margin-top: 100px;
		background: #D7D7D7;
		.string {
			display: flex;
			min-width: 150px;
			.cell {
				min-width: 60px;
				height: 40px;
				margin-left: auto;
			}
		}
		.buttons {
			button {
				margin-right: 10px;
			}
		}
	}
}
</style>
