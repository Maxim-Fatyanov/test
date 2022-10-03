<template>
    <div v-if="!visible" class="cookie">
        <div class="cookie-title">{{ title }}</div>
        <div class="cookie-text">{{ text }}</div>
        <ui-button @click="writeCookie(true)" class="cookie-button">{{ button }}</ui-button>
    </div>
</template>

<script setup lang="ts">

import {ref, onBeforeMount, defineProps} from "vue";
import UiButton from '../buttons/UiButton.vue';

defineProps({

    title: String,

    text: String,

    button: String,

})

const haveAcceptCookie = 'haveAcceptCookie';

const visible = ref(false);

const getCookie = (name: string) => {
    const matches = document.cookie.match(new RegExp(
        "(?:^|; )" + name.replace(/([.$?*|{}()\[\]\\\/+^])/g, '\\$1') + "=([^;]*)"
    ));

    return matches ? decodeURIComponent(matches[1]) : undefined;
};

const writeCookie = (value: boolean) => {
    const date = new Date;
    date.setDate(date.getDate() + 30);
    document.cookie = `${haveAcceptCookie}=${value}; expires=${date.toUTCString()}`;
    visible.value = true;
};

onBeforeMount(() => {
    visible.value = !!getCookie(haveAcceptCookie);
    visible.value && writeCookie(true);
})

</script>

<style lang="scss">
@import "../../styles/vars";

.cookie {
    position: fixed;
    max-width: 360px;
    padding: 20px;
    bottom: 15px;
    left: 15px;
    right: 15px;
    z-index: 10;
    background: #F4F5F5;
    border: 1px solid rgba($uiGrey, 0.2);
    border-radius: $uiBorderRadiusMedium;

    &-title {
        font-size: 32px;
        font-weight: 600;
    }

    &-text {
        margin: 16px 0;
        font-size: 16px;
        line-height: 1.5;
    }

    &-button {
        font-size: 16px;
    }
}

</style>