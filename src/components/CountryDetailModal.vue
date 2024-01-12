<script>

export default {
    data() {
        return {
            country: this.$parent.country,
        };
    },
    props: {
        togglePopup: Function,
        popupTrigger: Object,
    },
    methods: {
        close() {
            this.togglePopup();
        },
    }
}
</script>

<template>
    <div class="fixed top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 w-full h-full flex justify-center items-center
        bg-white/[0.44] backdrop-opacity-[1.5]">
        <div class="bg-white max-w-1/2">
            <div
                class="overflow-y-auto overflow-x-hidden justify-center items-center w-fullmd:inset-0 h-[calc(100%-1rem)] max-h-full">
                <div class="relative p-4 max-h-full w-full">
                    <div class="relative bg-white rounded-lg shadow dark:bg-gray-700 w-full">
                        <button type="button"
                            class="absolute top-3 end-2.5 text-gray-400 bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg text-sm w-8 h-8 ms-auto inline-flex justify-center items-center dark:hover:bg-gray-600 dark:hover:text-white"
                            data-modal-hide="progress-modal">
                            <i @click="close()" class="fa-solid fa-xmark text-[#243673] text-[36px]"></i>
                            <span class="sr-only">Close modal</span>
                        </button>
                        <div class="p-4 md:p-5">
                            <img class="h-[75px] text-gray-400 dark:text-gray-500 mb-4" :src="this.country.flags.png">
                            <h3 class="mb-2 text-xl font-bold text-gray-900 dark:text-white">{{ this.country.name.official
                            }} ({{ this.country.cca2 }}/{{ this.country.cca3 }})</h3>
                            <div class="grid grid-cols-2 gap-x-[50px]">
                                <p class="text-gray-700 dark:text-gray-400 mb-1">Independent: {{ this.country.independent }}
                                </p>
                                <p class="text-gray-700 dark:text-gray-400 mb-1">Region: {{ this.country.region }}</p>
                                <p class="text-gray-700 dark:text-gray-400 mb-1">Area: {{ this.country.area }}</p>
                                <p class="text-gray-700 dark:text-gray-400 mb-1">Population: {{ this.country.population }}
                                </p>
                                <div class="mb-1">
                                    <div v-for="(capital, id) in this.country.capital" :key="id">
                                        <p class="text-gray-700 dark:text-gray-400">Capital: {{ capital }}</p>
                                    </div>
                                </div>
                                <div class="mb-2">
                                    <div class="text-gray-700 dark:text-gray-400 inline">Currencies: </div>
                                    <div v-for="(currency, id, index) in this.country.currencies" :key="id" class="inline">
                                        <p class="text-gray-700 dark:text-gray-400 inline">
                                            <span v-if="index != 0">, </span>
                                            {{ id }}
                                        </p>
                                    </div>
                                </div>
                            </div>

                            <div class="mb-2">
                                <div class="text-gray-700 dark:text-gray-400 inline">Timezone: </div>
                                <div v-for="(timezone, id) in this.country.timezones" :key="id" class="inline">
                                    <p class="text-gray-700 dark:text-gray-400 inline">
                                        <span v-if="id != 0">, </span>
                                        {{ timezone }}
                                    </p>
                                </div>
                            </div>

                            <div class="mb-2">
                                <div class="text-gray-700 dark:text-gray-400 inline">Language: </div>
                                <div v-for="(language, id, index) in this.country.languages" :key="id" class="inline">
                                    <p class="text-gray-700 dark:text-gray-400 inline">
                                        <span v-if="index != 0">, </span>
                                        {{ language }}
                                    </p>
                                </div>
                            </div>

                            <div class="mb-2">
                                <div class="font-semibold text-[18px] text-gray-700 dark:text-gray-400 mb-1">Native Country:</div>
                                <div v-for="(nativeName, id) in country.name.nativeName" :key="id">
                                    <p class="font-normal text-gray-700 dark:text-gray-400"><span class="font-semibold">{{ id }}:</span> {{
                                    nativeName.official }}</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

        </div>

    </div>
</template>