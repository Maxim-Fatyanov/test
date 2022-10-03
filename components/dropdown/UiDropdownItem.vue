<template>
    <div class="dropdown-item h3" :class="{delimiter, disabled, red}" @click="updateMenuVisible()">
        <slot/>
    </div>
</template>

<script setup lang="ts">

import {defineProps, inject} from "vue";

defineProps({

    delimiter: {
        type: Boolean,
        default: false
    },

    disabled: {
        type: Boolean,
        default: false
    },

    red: {
        type: Boolean,
        default: false
    },

});

const updateMenuVisible = inject('UiDropdown:update') as () => void;

</script>

<style lang="scss" scoped>

@import "../../styles/vars";

.dropdown-item {
    position: relative;
    min-width: 140px;
    padding: 12px 20px;
    cursor: pointer;
    font-weight: 600;
    transition: all $uiTransition;
    color: $uiDark;
    white-space: nowrap;

    &:hover {
        background: rgba($uiGrey, 0.05);
    }

    &.delimiter {
        border-bottom: 1px solid $uiBackgroundLight;
    }

    &.red {
        color: $uiOrange;
    }

    &.disabled {
        cursor: default;
        color: $uiGrey;
        font-weight: 500;
        background: transparent;
    }
}

</style>