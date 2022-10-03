<template>
    <div :class="{'icon-show-more': chevron}" @click="updateMenuVisibleClick" ref="dropdown" class="dropdown">
        <slot/>
    </div>
</template>

<script setup lang="ts">

import {defineProps, provide, ref} from "vue";
import {useOutsideClick} from "../../hooks/useOutsideClick";

defineProps({

    chevron: {
        type: Boolean,
        default: true,
    },

})

const dropdown = ref<HTMLDivElement>();
const isMenuShown = ref(false);
const isTransform = ref(false);

const updateMenuVisibleClick = () => {
    isMenuShown.value = !isMenuShown.value;
    if (!dropdown.value) {
        return;
    }

    isTransform.value = window.innerWidth > 767 && window.innerHeight - (dropdown.value?.offsetHeight + dropdown.value?.getBoundingClientRect().y) < 260;
};

provide("UiDropdown:update", updateMenuVisibleClick);
provide("UiDropdown:isShown", isMenuShown);
provide("UiDropdown:isTransform", isTransform);

useOutsideClick(() => isMenuShown.value = false);

</script>

<style lang="scss" scoped>

@import "../../styles/vars";

.dropdown {
    display: flex;
    align-items: center;
    position: relative;
    cursor: pointer;

    &.icon-show-more {
        padding-right: 18px;
        justify-self: baseline;

        &:before {
            position: absolute;
            right: 0;
            top: 50%;
            font-size: 5px;
            color: $uiGrey;
            transform: translateY(-50%);
        }
    }
}

</style>

