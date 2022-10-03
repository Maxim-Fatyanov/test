<template>
    <div ref="selectBox" class="input-wrap icon-show-more" @click="toggleSelectList" :class="{'input-wrap_active': showSelectList}">
        <input v-if="searchable && showSelectList" @keydown="handleSearch" ref="searchInput" v-model="search" class="input" :class="{invalid}" @click.stop>
        <span v-else class="input h3" :class="{active: showSelectList, invalid}">
            <template v-if="selected">
                <slot name="item" v-bind.prop="{item: selected}">{{ getTitle(selected) }}</slot>
                <i v-if="clearable && model" @click.stop="clearSelection" class="icon-close"/>
            </template>
        </span>
        <span class="input-label" :class="{required, active: model}">{{ label }}</span>
        <div v-if="showSelectList" :class="{'select-list_transform': isTransform}" class="select-list">
            <div ref="selectList" class="select-list-wrap">
                <div v-for="(item, index) in data" :key="index" @click.stop="selectElement(item)" @keydown.enter="selectElement(item)" :class="{selected: isSelected(item)}" class="select-item" tabindex="0">
                    <slot name="item" v-bind.prop="{item}">{{ getTitle(item) }}</slot>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">

import {defineComponent, onUpdated, PropType, Ref, ref} from 'vue';
import {SelectItem, SimpleSelectItem, StringifyMethod, useSelect} from "./useSelect";

export default defineComponent({

    emits: ['update:modelValue', 'change', 'search'],

    props: {

        id: String,

        name: String,

        modelValue: {
            type: [Number, String, Object] as PropType<SelectItem>,
            default: '',
        },

        options: {
            type: Array as PropType<SimpleSelectItem[]>,
            required: true,
        },

        label: String,

        searchable: {
            type: Boolean,
            default: false,
        },

        clearable: {
            type: Boolean,
            default: false,
        },

        required: {
            type: Boolean,
            default: false,
        },

        invalid: {
            type: Boolean,
            default: false,
        },

        valueKey: String,

        titleKey: [String, Function] as PropType<StringifyMethod | string>,

    },

    setup(props, context) {

        const select = useSelect(context, props);

        const selectBox = ref<HTMLInputElement>() as Ref<HTMLInputElement>;
        const selectList = ref<HTMLInputElement>() as Ref<HTMLInputElement>;
        const searchInput = ref<HTMLInputElement>() as Ref<HTMLInputElement>;

        const isTransform = ref(false);

        onUpdated(() => {
            searchInput.value && searchInput.value.focus();
            isTransform.value = window.innerWidth > 767 && window.innerHeight - (selectBox.value?.getBoundingClientRect().y + selectBox.value.offsetHeight) < 270;

            if (!selectList.value) {
                return
            }

            selectList.value.scrollTop = isTransform.value ? selectList.value?.scrollHeight : 0;
        });

        return {
            ...select,
            selectList,
            selectBox,
            isTransform,
            searchInput,
        }
    },

})

</script>

<style lang="scss" scoped>

@import "../../styles/vars";
@import "./inputs";
@import "./selects";

.select-list {

    &_transform {
        top: auto;
        bottom: calc(100% + 5px);;
    }
}

.input {

    .icon-close {
        padding: 5px;
        position: absolute;
        top: 20px;
        right: 35px;
        cursor: pointer;

        &:before {
            font-size: 10px;
            color: $uiGrey;
            pointer-events: none;
        }
    }
}

</style>
