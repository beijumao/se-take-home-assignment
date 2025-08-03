<script setup>
import { ref, onMounted, watch } from 'vue'

const props = defineProps({
    botName: {
        type: String,
        required: true
    },
    normalOrder: {
        type: Array,
        required: true
    },
    vipOrder: {
        type: Array,
        required: true
    },
    active: {
        type: Boolean,
        default: true
    }
})

const emits = defineEmits(['shift', 'complete', 'revert'])

onMounted(() => {
    if (props.vipOrder.length > 0) {
        getTopVipOrder()
        initiateProcess()
    } else if (props.normalOrder.length > 0) {
        getTopNormalOrder()
        initiateProcess()
    }
})

const countdown = ref(0)

const currentOrder = ref(null)

function processOrder() {
    let intervalId = setInterval(() => {
        countdown.value--
        if (countdown.value <= 0) {
            clearInterval(intervalId)
            intervalId = null

            emits('complete', currentOrder.value)

            if (props.vipOrder.length > 0) {
                getTopVipOrder()
                initiateProcess()
            } else if (props.normalOrder.length > 0) {
                getTopNormalOrder()
                initiateProcess()
            } else {
                currentOrder.value = null
            }
        }
    }, 1000)
}

function initiateProcess() {
    countdown.value = 10
    processOrder()
}

function getTopVipOrder() {
    currentOrder.value = props.vipOrder[0]
    emits('shift', 'VIP')
}

function getTopNormalOrder() {
    currentOrder.value = props.normalOrder[0]
    emits('shift', 'NRM')
}

watch(() => props.vipOrder.length, (value) => {
    if (!currentOrder.value && value > 0) {
        getTopVipOrder()
        initiateProcess()
    }
});

watch(() => props.normalOrder.length, (value) => {
    if (!currentOrder.value && props.vipOrder.length <= 0 && value > 0) {
        getTopNormalOrder()
        initiateProcess()
    }
});

watch(() => props.active, (value) => {
    if (!value) {
        emits('revert', currentOrder.value)
    }
});
</script>

<template>
    <div>
        <v-card class="pa-4 text-center" width="100" variant="tonal" :color="currentOrder ? 'primary' : ''">
            <div class="font-weight-bold">Bot {{ botName }}</div>
            <div v-if="currentOrder" class="text-caption">Processing {{ currentOrder }}, {{ countdown }}s left</div>
            <div v-else>Idle</div>
        </v-card>
    </div>
</template>

<style scoped></style>
