<template>
    <div :class="['d-collapse-item', expand ? 'expand' : '']">
        <div class="d-collapse-item__title" @click="handleItemClick">
            <d-icon name="chevron-right" class="d-collapse-item__arrow"></d-icon>
            <slot name="title"></slot>
            <template v-if="!$slots.title">
                {{title}}
            </template>
        </div>
        <collapse-transition>
            <div class="d-collapse-item__content" v-show="expand">
                <slot></slot>
                <slot name="content"></slot>
                <template v-if="!$slots.content">
                    {{content}}
                </template>
            </div>
        </collapse-transition>
    </div>
</template>
<script>
import DIcon from '../icon/icon';
import EventEmitter from '../mixins/event_emitter';
import CollapseTransition from '../transition/collapse-transition.vue';

export default {
    name: 'DCollapseItem',
    components: { DIcon, CollapseTransition },
    mixins: [EventEmitter],
    data() {
        return {
            expand: false,
            showContent: false,
            timer: null,
        }
    },
    props: {
        title: {
            type: String,
            default: '',
        },
        content: {
            type: String,
            default: '',
        },
    },
    methods: {
        handleItemClick() {
            if (this.timer) {
                clearTimeout(this.timer);
            }
            this.timer = setTimeout(_ => {
                this.showToggle();
            }, 200);
        },
        showToggle() {
            this.expand = !this.expand;
            this.dispatch('DCollapse', 'item-click', this);
        },
    },
    mounted() {
        this.subscribe('sibling-item-click', vnode => {
            if (this !== vnode && this.expand) {
                this.expand = false;
            }
        });
    },
}
</script>