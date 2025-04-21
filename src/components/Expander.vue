<template>
    <div
        class="expander"
        ref="expander"
        :style="shellStyles"
    >
        <span
            class="start"
            :class="{ 'hide-content': shouldShowReveal }"
            ref="start"
        >
            <slot />
        </span>
        <span
            class="end"
            :class="{ 'hide-content': !shouldShowReveal }"
            ref="end"
        >
            <slot name="reveal" />
        </span>
    </div>
</template>

<script setup>
import { useTemplateRef, onMounted, ref, computed, watch } from 'vue';
const props = defineProps({
    reveal: {
        default: false,
        type: Boolean,
    },
});
const expander = useTemplateRef('expander');
const startContainer = useTemplateRef('start');
const endContainer = useTemplateRef('end');

const expansionStates = {
    START: 1,
    CLOSED: 2,
    REVEALED: 3,
};

const startWidth = ref(0);
const endWidth = ref(0);

const readyToAnimate = ref(false);

const currentState = ref(expansionStates.START);


const containerWidth = computed(() => {
    let width = startWidth.value;
    switch (currentState.value) {
        case expansionStates.START: {
            width = startWidth.value;
            break;
        }
        case expansionStates.CLOSED: {
            width = 0;
            break;
        }
        case expansionStates.REVEALED: {
            width = endWidth.value;
            break;
        }
    }
    return width;
});

const shouldShowReveal = computed(() => currentState.value === expansionStates.REVEALED);

const trackWidths = async () => {
    if (startWidth.value > 0) {
        readyToAnimate.value = true;
    }
    startWidth.value = startContainer?.value?.getBoundingClientRect()?.width;
    endWidth.value = endContainer?.value?.getBoundingClientRect()?.width;
    await new Promise(resolve => setTimeout(resolve, 10));
    if (expander.value && expander.value.parentNode) {
        trackWidths();
    }
}

const aninate = async () => {
    await new Promise(resolve => setTimeout(resolve, 100));
    currentState.value = expansionStates.CLOSED;
    await new Promise(resolve => setTimeout(resolve, 1000));
    currentState.value = expansionStates.REVEALED;
}

const shouldAnimate = computed(() => props.reveal);

const shellStyles = computed(() => {
    const styles = {
        width: `${containerWidth.value}px`,
    };
    if (readyToAnimate.value) styles.transition = 'width 1s';
    return styles;
});

watch(shouldAnimate, (newVaue) => {
    if (newVaue) aninate();
});

onMounted(() => {
    trackWidths();
})

</script>

<style>
.expander{
    font-size: 1vw;
    position: absolute;
    background-color: #00cc00;
    height: 1.5vw;
    overflow: hidden;
}
.expander .start,
.expander .end{
    position: absolute;
    top: 0;
    left: 0;
    display: block;
    padding: 0 2vw;
    white-space: nowrap;
}

.hide-content{
    opacity: 0;
}

</style>