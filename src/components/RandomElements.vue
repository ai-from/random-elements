<template>
    <div>
        <div class="re__title">Random Elements</div>
        <div class="re__elements">
            <div
                class="re__row"
                v-for="(row, index) in randomRows()"
                :key="`re-row-${index}`"
                ref="reRow"
            >
                <div
                    class="re__element"
                    v-for="(element, index2) in randomColumns()"
                    :key="`re-element-${index2}`"
                >
                    {{ randomNumber() }}
                </div>
            </div>
        </div>
    </div>
</template>


<script>
export default {
    name: 'RandomElements',
    data() {
        return {
            limits: {
                rows: {min: 101, max: 200},
                columns: {min: 11, max: 20},
                el: {min: 0, max: 999}
            },
            time: 1000,
            currentRows: []
        }
    },
    methods: {
        random(obj = {min: 0, max: 0}) {
            return Math.floor (Math.random () * (obj.max - obj.min + 1)) + obj.min;
        },
        randomRows() {
            return this.random(this.limits.rows);
        },
        randomColumns() {
            return this.random(this.limits.columns);

        },
        randomNumber() {
            return this.random(this.limits.el);
        },
        updateElement(row, updateIdx) {
            row.children[updateIdx].classList.add('update');
            row.children[updateIdx].innerText = this.random(this.limits.el);
            setTimeout(() => row.children[updateIdx].classList.remove('update'), this.time);
        },
        setCurrentRows() {
            const rows = this.$refs.reRow
            this.currentRows = [];
            rows.forEach(row => {
                const rect = row.getBoundingClientRect();
                if((rect.top >= 0) && (rect.bottom <= window.innerHeight)) {
                    this.currentRows.push(row)
                }
            });
        },
        update() {
            this.setCurrentRows();
            setInterval(() => {
                if(this.currentRows) {
                    this.currentRows.forEach(row => {
                        const updateIdx = this.random({min: 0, max: row.children.length - 1});
                        this.updateElement(row, updateIdx);
                    });
                }
            }, this.time)
        }
    },
    mounted() {
        this.update();
        ['resize', 'scroll'].forEach(evt => {
            window.addEventListener(evt, () => this.setCurrentRows());
        });
    }
}
</script>

<style lang="sass" scoped>
.re
    &__title
        text-align: center
        margin: 0 0 20px
    &__elements
        overflow: auto
    &__row
        display: flex
        column-gap: 10px
        margin: 0 0 10px
    &__element
        display: flex
        justify-content: center
        align-items: center
        min-width: 30px
        min-height: 30px
        border-radius: 4px
        border: 1px solid grey
        transform: scale(1)
        transition: all linear .25s
        cursor: default
        &.update
            background: grey
        &:hover
            transform: scale(0.8)
</style>