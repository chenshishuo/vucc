<!--
  tabs 选项卡组件

  @param {String} [type=default|primary|outline|error|link] 按钮的类型
  @param {String} [size=normal|large|small] 按钮的大小
  @param {String} appendClass 自定义class
  @param {Object} appendClass 自定义Style对象
-->
<template>
    <!-- tabs头部-->
    <div :class="['tbd-tabs tbd-tabs-top', type, appendClass, size]">
        <div class="tbd-tabs-bar">
            <!-- 额外操作 -->
            <div class="tbd-tabs-extra-content">
                <slot name="operate"></slot>
            </div>

            <!-- 内容头部分 -->
            <div class="tbd-tabs-nav-container" >
                <div class="tbd-tabs-nav-wrap" >
                    <div class="tbd-tabs-nav-scroll">
                        <div class="tbd-tabs-nav" @click="selectTab(curIndex, $event)">
                            <slot name="header"></slot>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 切换tab内容部分 -->
        <div class="tbd-tabs-content" >
            <slot></slot>
        </div>
    </div>
</template>

<script>
    import {componentBaseParamConfig} from '../base-config';

    export default {
        props: Object.assign({}, componentBaseParamConfig, {
            type: {
                type: String,
                default: 'line'
            },
            size: {
                type: String,
                default: 'normal'
            }
        }),

        data() {
            return {
                curIndex: 0
            }
        },

        methods: {
            selectTab(index, event) {
                if(!event) {
                    event = {};
                    event.target = this.$el.querySelector('.tbd-tabs-nav > [slot=header]').children[index];
                    event.target.className += ' tbd-tabs-tab-active';
                    return;
                }

                const selectIndex = +(event.target.getAttribute('tab_index'));
                if(selectIndex === null || (event.type && selectIndex === index)) return;

                // switch header
                this.$el.querySelector('.tbd-tabs-tab-active').className = this.$el.querySelector('.tbd-tabs-tab-active').className.replace(/\s?tbd-tabs-tab-active\s?/, '');
                event.target.parentNode.className += ' tbd-tabs-tab-active ';

                // switch context
                let curContextEl = this.$el.querySelector('.tbd-tabs-content :nth-child(' + (selectIndex + 1) + ')');
                curContextEl.setAttribute('class',  curContextEl.className.replace(/\s?tbd-tabs-tabpane-hidden\s?/, ''));
                let oldContextEl = this.$el.querySelector('.tbd-tabs-content :nth-child(' + (index + 1) + ')');
                oldContextEl.setAttribute('class',  oldContextEl.className + ' tbd-tabs-tabpane-hidden ');

                this.curIndex = selectIndex;
            }
        },

        ready() {
            this.type = {
               'line' : 'tbd-tabs-line',
               'card' : 'tbd-tabs-card',
               'panel' : 'tbd-tabs-panel'
            }[this.type];

            this.size = {
               'normal': '',
               'large': 'tbd-tabs-lg',
               'small': 'tbd-tabs-sm'
            }[this.size];

            // header slot 样式填充和插入,并绑定事件
            const tabHeader = this.$el.querySelector('.tbd-tabs-nav > [slot=header]');
            let temp = '';
            let _this = this;

            tabHeader.children && Array.from(tabHeader.children).forEach(function(el, index) {
                el.className += ' tbd-tabs-tab-inner ';
                el.setAttribute('tab_index', index);

                if(el.hasAttribute('active')) {
                    el.removeAttribute('active');

                    _this.curIndex = +index;
                    temp += '<div class="tbd-tabs-tab tbd-tabs-tab-active" >' + el.outerHTML + '</div>';
                } else {
                    temp += '<div class="tbd-tabs-tab" >' + el.outerHTML + '</div>';
                }
            });

            tabHeader.innerHTML = temp;

            // panes class 绑定
            const tabPanes = this.$el.querySelector('.tbd-tabs-content');
            let panes = '';
            tabPanes.children && Array.from(tabPanes.children).forEach(function(el, index) {
                if(index !== _this.curIndex) {
                    el.className += ' tbd-tabs-tabpane-hidden ';
                }

                panes += el.outerHTML;
            });

            tabPanes.innerHTML = panes;

            if(_this.curIndex === 0) _this.selectTab(0);
        }
    }
</script>

<style scoped>
    @import "style.css";
</style>