<template>
    <div v-if="isMenuShown" :class="{top, center, 'dropdown-menu-wrapped_transform': isTransform }" class="dropdown-menu-wrapped box_border">
        <div ref="dropdownMenu" @click="updateMenuVisible()" class="dropdown-menu">
            <slot/>
        </div>
    </div>
</template>

<script setup lang="ts">

import {defineProps, inject, ref, watch} from "vue";

defineProps({

    top: {
        type: Boolean,
        default: false
    },

    center: {
        type: Boolean,
        default: false
    },

});

const dropdownMenu = ref<HTMLDivElement>();

const isMenuShown = inject('UiDropdown:isShown');
const isTransform = inject('UiDropdown:isTransform');
const updateMenuVisible = inject('UiDropdown:update') as () => void;

watch(() => isTransform, () => dropdownMenu.value && (dropdownMenu.value.scrollTop = isTransform ? dropdownMenu.value?.scrollHeight : 0));

</script>

<style lang="scss" scoped>

@import "../../styles/vars";

.dropdown-menu {
    width: 100%;
    padding: 15px 0;
    display: flex;
    flex-direction: column;
    max-height: 310px;
    overflow-y: scroll;
    background-color: $uiWhite;
    border-radius: $uiBorderRadiusMedium $uiBorderRadiusMedium 0 0;

    &-wrapped {
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        display: flex;
        align-items: flex-end;
        background-color: rgba($uiDark, 0.4);
        z-index: 11;
        box-shadow: 0 20px 50px rgb(0 21 36 / 5%);
    }
}

@media only screen and (min-width: 768px) {
    .dropdown-menu {
        width: 100%;
        height: 100%;
        padding: 6px 0;
        max-height: 360px;
        background-color: $uiWhite;

        &-wrapped {
            position: absolute;
            top: 100%;
            bottom: auto;
            left: auto;
            right: 0;
            border-radius: $uiBorderRadiusMedium;
            background-color: transparent;
            overflow: hidden;
            min-width: 170px;

            &_transform {
                top: auto;
                bottom: 100%;
            }

            &.center {
                transform: translateX(50%);
            }

            &.top {
                top: auto;
                bottom: calc(100% + 8px);
            }
        }
    }
}

</style>