<template>
    <div class="modal" @mousedown.prevent="">
        <div class="modal-wrapper">
            <div class="modal-content">
                <div class="string" v-for="(line, index) in formatedCells" :key="index">
                    <input
                        v-for="(cell, index) in line"
                        :key="index"
                        v-model="matrixCopy[cell.position.line][cell.position.index]"
                        type="checkbox"
                    />
                </div>
            </div>
            <div>
                <button @click="close">Отмена</button>
                <button @click="accept">Применить</button>
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
    },
    data() {
        return {
            cells: [...this.data],
            matrixCopy: [...this.matrix.map(arr => [...arr])]
        }
    },
    computed: {
        linesCount() {
            return this.cells[this.cells.length - 1].position.line - this.cells[0].position.line + 1
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
        margin-top: 100px;
        background: gray;
    }
    
}
</style>