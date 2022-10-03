<template>
    <transition name="fade">
        <div v-if="modelValue" ref="modal" class="modal" @click="onWrapperClick">
            <div :class="{large}" class="box modal-content">
                <span v-if="!permanent" class="modal-close icon-close" @click.stop="onClose"/>
                <h2 v-if="slots.title" class="h1">
                    <slot name="title"/>
                </h2>
                <slot/>
                <div v-if="slots.footer" class="modal-footer">
                    <slot name="footer"/>
                </div>
            </div>
        </div>
    </transition>
</template>

<script setup lang="ts">

import {defineEmits, defineProps, onBeforeUnmount, onMounted, ref, useSlots, watch} from "vue";

let scrollTop = 0;

const emit = defineEmits(['open', 'close', 'update:modelValue']);

const props = defineProps({

    permanent: {
        type: Boolean,
        default: false,
    },

    stable: {
        type: Boolean,
        default: false,
    },

    large: {
        type: Boolean,
        default: false,
    },

    modelValue: {
        type: Boolean,
        default: () => true,
    },

});

const modal = ref<HTMLDivElement>();
const onToggle = (value: boolean) => {
    value && (scrollTop = window.pageYOffset);
    document.body.style.top = value ? `-${scrollTop}px` : '';
    document.body.style.marginRight = value ? `${window.innerWidth - document.body.clientWidth}px` : '';
    document.body.classList.toggle('fix', value);
    !value && window.scroll(0, scrollTop);
};
const onClose = () => {
    emit('update:modelValue', false);
    onToggle(false);
    emit('close');
};
const onWrapperClick = (event: PointerEvent) => !props.permanent && !props.stable && modal.value && event.target === modal.value && onClose();

onMounted(() => onToggle(props.modelValue));

onBeforeUnmount(() => onToggle(false));

const slots = useSlots();

watch(() => props.modelValue, value => {
    onToggle(value);
    value ? emit('open') : emit('close');
});

</script>

<style scoped lang="scss">

@import "../../styles/vars";

.modal {
    -webkit-overflow-scrolling: touch;

    display: grid;
    grid-template-columns: 1fr;
    justify-content: center;
    align-items: center;
    z-index: 11;

    background: rgba($uiDark, 0.4);

    margin: auto;
    position: fixed;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;

    overflow-y: scroll;
    overflow-x: hidden;

    &-close {
        position: absolute;
        top: 12px;
        right: 12px;
        width: 30px;
        height: 30px;
        line-height: 30px;
        text-align: center;
        cursor: pointer;
        transition: background-color $uiTransition;
        border-radius: $uiBorderRadiusSmall;

        &:hover {
            background: rgba($uiGrey, 0.05);
        }
    }

    &-content {
        position: relative;
        width: 100%;
        max-width: 530px;
        border-radius: 0;

        &.large {
            max-width: 1199px;
        }
    }

    &-footer {
        display: flex;
        flex-flow: row nowrap;
        gap: 20px;
        margin-top: 34px;

        :deep(.btn) {
            width: 100%;
        }
    }

    .h1 {
        margin: 0 30px 20px 0;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
    }
}

@media screen and (min-width: 480px) {
    .modal {
        padding: 20px;

        &-content {
            border-radius: $uiBorderRadiusMedium;
            margin: auto;
        }
    }
}

</style>