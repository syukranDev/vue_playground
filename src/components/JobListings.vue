<script setup>
import JobListingSingle from '@/components/JobListingSingle.vue';
import { ref, defineProps, onMounted, reactive } from 'vue';
import { RouterLink } from 'vue-router';
import PulseLoader from 'vue-spinner/src/PulseLoader.vue'
import axios from 'axios';

defineProps({
    // limit : {
    //     type: Number
    // }, // or can write as below
    limit: Number,
    showButton: {
        type: Boolean,
        default: false
    }
})

// Option 1 
//const jobs = ref([]); //macam useState react, set default value first
//guna reactive pun boleh instead of ref, 
// 1) reactive 
//     -can take objects only
//     -cannot be reassign
// 2) ref  -- this is RECOMENDDED since we can use isLoading value for spinner etc
//    -can take objects or primitive (string, numbers, boolean)
//    -can reassign using <var>.value = 'new value'

// Option 2
const state = reactive({
    jobs: [],
    isLoading: true
})

onMounted(async () => { //macam useEffect react, to call api resonse.
    try {
        const response = await axios('http://localhost:5000/jobs')
        // jobs.value = response.data -- Option 1
        state.jobs = response.data // options 2
        state.isLoading = false
    } catch (e) {
        console.log('Error fetch jobs data', e.message)
    } 
})
</script>

<template>
    <section class="bg-blue-50 px-4 py-10">
        <div class="container-xl lg:container m-auto">
            <h2 class="text-3xl bold text-green-500 mb-6 text-center">Browse Jobs</h2>
            
            <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
                <PulseLoader/>
            </div>

            
            <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <JobListingSingle v-for="job in state.jobs.slice(0, limit || state.jobs.length)" :keys="state.jobs.id" :job="job"/>
            </div>
        </div>
    </section>

    <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
      <RouterLink
        to="/jobs"
        class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
        >View All Jobs
    </RouterLink>
    </section>
</template>