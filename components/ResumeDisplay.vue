<template>
    <div>
        <template v-if="resumeModel.loading">
            <p>Загрузка данных резюме...</p>
        </template>

        <template v-else-if="resumeModel.error">
            <p class="text-red-600">Ошибка: {{ resumeModel.error }}</p>
        </template>

        <template v-else>
            <h1>Резюме: {{ resumeModel.data.name }}</h1>
            <!-- Остальной вывод резюме -->
        </template>
    </div>
</template>

<script setup lang="ts">
const config = useRuntimeConfig();
const apiBaseUrl = config.public.apiBase;

const resume = ref<any[]>([]);
const error = ref<string | null>(null);

// Resume model computed from mock data
const resumeModel = computed(() => {
    if (error.value) {
        return { loading: false, error: error.value, data: null };
    }
    if (!resume.value || Object.keys(resume.value).length === 0) {
        return { loading: true, error: null, data: null };
    }
    return { loading: false, error: null, data: resume.value };
});

// Fetch mock resume
onMounted(async () => {
    try {
        const response = await fetch(`${apiBaseUrl}/test/v2/app`);
        if (!response.ok) throw new Error('Failed to fetch resume');
        const data = await response.json();
        resume.value = data || [];
    } catch (err) {
        console.error('Error fetching resume:', err);
        error.value = 'Failed to load resume';
    }
});
</script>
