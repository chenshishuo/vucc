<!--
   分页栏 组件

   @param {Boolean} isShow 是否显示分页栏组件
-->

<template>
    <div class="tbd-pagination" v-if="isShow">
        <div class="tbd-pagination-records">
            共<em>{{total}}</em>条记录，每页显示
            <v-select :data="pageSizeObject" :value.sync="pageSize"></v-select>
            条
        </div>

        <div class="tbd-pagination-btns">
            <a class="tbd-pagination-btns-first"
               href="javascript:void(0);"
               @click="selectPage(1)"></a>

            <a class="tbd-pagination-btns-prev"
               href="javascript:void(0);"
               @click="jumpPrev"></a>

            <a v-for="num in pageNums"
               track-by="$index"
               href="javascript:void(0);"
               :class="{'tbd-pagination-btns-dot': num == '...',
                'tbd-pagination-btns-active': currentPage == num}"
               @click="selectPage(num)">{{num}}</a>

            <a class="tbd-pagination-btns-next"
               href="javascript:void(0);"
               @click="jumpNext"></a>

            <a href="javascript:void(0);"
               class="tbd-pagination-btns-last"
               @click="selectPage(totalNum)"></a>
            <span class="tbd-pagination-btns-records">共<em>{{totalNum}}</em>页</span>
        </div>
    </div>
</template>

<script>
    import vSelect from '../select/';

    export default {
        props: {
            isShow: {
                type: Boolean,
                default: true
            },
            onChange: {
                type: Function
            },
            pageSize: {
                type: Number,
                default: 5
            },
            currentPage: {
                type: Number,
                default: 1
            },
            total: {
                type: Number,
                default: 0
            }
        },

        data () {
            return {
                pageSizeObject: {
                    optsList: [{
                        value: 5
                    }, {
                        value: 10
                    }, {
                        value: 15
                    }]
                }
            }
        },

        components: {
            vSelect
        },

        computed: {
            totalNum() {
                return Math.ceil(this.total / this.pageSize)
            },

            pageNums() {
                // 测试 用例
                // current=7 => 1,2 ...5,6,7,8,9 ...,25
                // current=3 => 1,2,3,4,5...,25
                // current=25 => 1,2,...,23,24,25

                let pageNums = [1,2];

                let totalNum = this.totalNum;

                let beginIndex = this.currentPage - 2;
                let endIndex = this.currentPage + 2;
                beginIndex = beginIndex < 1 ? 1 : beginIndex;
                endIndex = endIndex > totalNum ? totalNum : endIndex;

                if (totalNum < 3) {
                    // 没有 ... 的情况
                    pageNums = [];
                    for (let index = beginIndex; index < endIndex+1; index++) {
                        pageNums.push(index);
                    }

                } else {

                    if (beginIndex - 2 > 1) {
                        // add ...
                        pageNums.push('...');
                    }

                    for (let index = beginIndex; index < endIndex + 1; index++ ) {
                        if (index > 2 && index < totalNum) {
                            pageNums.push(index);
                        }
                    }

                    if (totalNum - endIndex > 1) {
                        pageNums.push('...');
                    }

                    pageNums.push(totalNum);
                }

                return pageNums;
            }
        },

        methods: {
            selectPage(num) {
                if (num === '...') {
                    return;
                }
                this.currentPage = num;
                this.onChange(this.currentPage)
            },

            jumpPrev() {
                this.currentPage = (this.currentPage - 1 > 0 ? this.currentPage - 1 : 1);
                this.onChange(this.currentPage)

            },

            jumpNext() {
                this.currentPage = (this.currentPage + 1 > this.totalNum ? this.totalNum : this.currentPage + 1 );
                this.onChange(this.currentPage)

            }
        }
    }
</script>

<style scoped>
    @import "style.css";
</style>
