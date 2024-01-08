<template>
    <button @click="clearTimes" @keydown.space.prevent="preventSpace">
        Clear times
    </button>
    <div class="times">
        <p class="time" v-for="t in reversedTimes" :key="t">{{ t.toFixed(2) }}</p>
    </div>
    <div class="main">
        <h1>{{ readyText }}</h1>
        <div class="content">
            <h2>{{ time.toFixed(2) }}</h2>
        </div>
    </div>
</template>

<script setup>
    import { ref } from 'vue';
    import nuxtStorage from 'nuxt-storage';


    const times = ref([])

    var storedTimes = nuxtStorage.localStorage.getData("times", times)

    if(storedTimes != null) {
        times.value = storedTimes
    }

    const readyText = ref("ready");
    const time = ref(0.00);
    let intervalId = null;

    onNuxtReady(() => {
        window.addEventListener('keyup', function(ev) {
            if (ev.code === "Space") {
                if (!intervalId) {
                    time.value = 0.00;

                    readyText.value = "GO";
                    intervalId = setInterval(function() {
                        time.value += 0.01;
                    }, 10);
                } else {
                    readyText.value = "ready";
                    clearInterval(intervalId);
                    intervalId = null;
                    times.value.push(time.value)
                    nuxtStorage.localStorage.setData("times", times.value)
                }
            }
        });
    });
    import { computed } from 'vue';
    const reversedTimes = computed(() => times.value.slice().reverse());

    async function clearTimes() {
        nuxtStorage.localStorage.clear("times")
        times.value = []
    }

    function preventSpace(event) {
        // Prevent the default behavior of the space key when the button is focused
        if (event.target.tagName.toLowerCase() === 'button') {
            event.preventDefault();
        }
    }
</script>

<style scoped>
    h1 {
        font-style: bold;
        text-align: center; /* Add this line to center the text horizontally */
        line-height: 1;    /* Add this line to remove any extra vertical spacing */
        align-self: center;
    }
    .main {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
    }

    .content {
        display: flex;
        flex-direction: column;
    }

    .times {
        display: flex;
        flex-direction: row;
        width: 250px;
        height: 200px;
        outline-style: solid;
        flex-wrap: wrap;
        overflow-y: scroll;
    }

    .time {
        margin-left: 10px; 
    }
</style> 