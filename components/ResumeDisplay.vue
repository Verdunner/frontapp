<template>
    <div>
        <template v-if="resumeModel.loading">
            <p>Загрузка данных резюме...</p>
        </template>

        <template v-else-if="resumeModel.error">
            <p class="text-red-600">Ошибка: {{ resumeModel.error }}</p>
        </template>

        <template v-else>
            <!-- Мета-контролы -->
            <div class="flex justify-start gap-2 mb-4">
                <UButton
                    title="Печать"
                    icon="i-heroicons-printer"
                    color="primary"
                    size="xl"
                    square
                />
                <UButton
                    title="Скачать PDF"
                    icon="i-heroicons-arrow-down-tray"
                    color="primary"
                    size="xl"
                    square
                />
                <UButton
                    title="Скачать DOC"
                    icon="i-heroicons-document-arrow-down"
                    color="primary"
                    size="xl"
                    square
                />
                <UButton
                    title="Редактировать"
                    icon="i-heroicons-pencil-square"
                    color="primary"
                    size="xl"
                    square
                />
                <UButton
                    title="Удалить"
                    icon="i-heroicons-trash"
                    color="primary"
                    size="xl"
                    square
                />
                <UButton
                    title="Отправить"
                    icon="i-heroicons-paper-airplane"
                    color="primary"
                    size="xl"
                    square
                />
                <UButton
                    title="В избранное"
                    icon="i-heroicons-star"
                    color="primary"
                    size="xl"
                    square
                />
            </div>

            <UCard>
                <!-- Профиль кандидата -->
                <div class="flex gap-6 items-start mb-4">
                    <!-- Аватар  -->
                    <div class="relative w-[200px] h-[200px]">
                        <UAvatar
                            :src="avatarUrl"
                            icon="i-heroicons-user"
                            class="w-full h-full shrink-0"
                        />
                        <div
                            v-if="avatarError"
                            class="absolute inset-0 flex items-center justify-center text-red-600 text-sm font-semibold bg-black/50 rounded-full text-center px-2"
                        >
                            Не получилось <br />
                            загрузить аватар
                        </div>
                    </div>

                    <!-- Текстовый блок -->
                    <div class="space-y-1">
                        <!-- ФИО -->
                        <h2 class="text-2xl font-bold">
                            {{ resumeModel.data.name }}
                        </h2>

                        <!-- Вакансия, дата отклика -->
                        <p>
                            {{ `Вакансия: ${resumeModel.data.listing_id}` }}
                            {{
                                `(${parseResponseDate(
                                    resumeModel.data.date
                                )} Отклик)`
                            }}
                        </p>

                        <!-- Статус "Просмотрено" -->
                        <UBadge color="success">
                            {{
                                resumeModel.data.status === 'viewed'
                                    ? 'Просмотрено'
                                    : 'Не просмотрено'
                            }}
                        </UBadge>

                        <!-- Возраст -->
                        <p>
                            {{
                                resumeModel.data.age
                                    ? `${resumeModel.data.age} лет`
                                    : 'Возраст не указан'
                            }}
                        </p>

                        <!-- Телефон -->
                        <div class="flex items-center gap-2">
                            <UIcon name="i-heroicons-phone" class="w-5 h-5" />
                            <span>
                                {{
                                    resumeModel.data.phone
                                        ? `${resumeModel.data.phone}`
                                        : 'Телефон не указан'
                                }}
                            </span>
                            <div
                                v-if="resumeModel.data.phone"
                                class="flex items-center gap-2 text-primary-500"
                            >
                                <UIcon
                                    name="i-simple-icons-whatsapp"
                                    class="w-5 h-5 hover:opacity-80 cursor-pointer"
                                />
                                <UIcon
                                    name="i-simple-icons-viber"
                                    class="w-5 h-5 hover:opacity-80 cursor-pointer"
                                />
                                <UIcon
                                    name="i-simple-icons-telegram"
                                    class="w-5 h-5 hover:opacity-80 cursor-pointer"
                                />
                            </div>
                        </div>

                        <!-- Почта -->
                        <div class="flex items-center gap-2">
                            <UIcon
                                name="i-heroicons-envelope"
                                class="w-5 h-5"
                            />
                            <span>
                                {{
                                    resumeModel.data.email
                                        ? `${resumeModel.data.email}`
                                        : 'Почта не указана'
                                }}
                            </span>
                        </div>
                    </div>
                </div>

                <!-- Дела -->
                <div class="mb-4">
                    <h3 class="text-lg font-semibold mb-2">Дела:</h3>
                    <div class="flex gap-4">
                        <UButton color="success" disabled
                            >Собеседование<br />запланировано</UButton
                        >
                        <UButton color="success" variant="outline"
                            >Создать<br />видеозвонок</UButton
                        >
                        <UButton color="success" variant="outline" disabled
                            >Запланировать<br />событие</UButton
                        >
                        <UButton color="success" variant="outline"
                            >Отправить<br />запрос</UButton
                        >
                    </div>
                </div>

                <!-- Статус рассмотрения -->
                <div class="mb-4">
                    <h3 class="text-lg font-semibold mb-2">
                        Статус рассмотрения:
                    </h3>
                    <div class="flex gap-4 flex-wrap">
                        <UBadge
                            color="success"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Новое</UBadge
                        >
                        <UBadge
                            color="success"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Просмотрено</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Отправлено<br />приглашение</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Назначено<br />собеседование</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Не дошёл</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Проведено<br />собеседование</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Ожидание ответа<br />соискателя</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[120px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Принятие решения</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Принят</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Отклонено/Отказ</UBadge
                        >
                        <UBadge
                            color="neutral"
                            variant="outline"
                            class="w-[140px] text-center justify-center"
                            trailing-icon="i-lucide-arrow-right"
                            >Архивировано</UBadge
                        >
                    </div>
                </div>

                <!-- Источник отклика -->
                <div class="mb-4">
                    <h3 class="text-lg font-semibold mb-2">Pikabu отклик</h3>
                    <p>Отклик с портала Pikabu</p>
                </div>

                <!-- Личные данные -->
                <div class="mb-4">
                    <p>
                        <strong>Дата рождения:</strong>
                        {{ resumeModel.data.birth_date }}
                    </p>
                    <p>
                        <strong>Гражданство:</strong>
                        {{ extractCitizenship(resumeModel.data.description) }}
                    </p>
                </div>

                <!-- Сопроводительное письмо -->
                <div class="mb-4">
                    <p>{{ extractAbout(resumeModel.data.description) }}</p>
                </div>

                <!-- Прикрепленные файлы -->
                <div
                    class="bg-secondary-100 dark:bg-secondary-900 rounded-xl p-4 flex items-center gap-4 mt-6"
                >
                    <!-- Иконка -->
                    <div
                        class="text-secondary-600 dark:text-secondary-300 text-4xl font-bold"
                    >
                        <UIcon
                            name="material-symbols:info-i-rounded"
                            class="size-5"
                        />
                    </div>

                    <!-- Контент -->
                    <div class="space-y-4">
                        <!-- Портфолио -->
                        <div>
                            <h4 class="font-semibold mb-1">
                                Файлы портфолио:
                                {{
                                    resumeModel.data.portfolios
                                        ? resumeModel.data.portfolios
                                        : ''
                                }}
                            </h4>
                        </div>

                        <!-- Резюме -->
                        <div>
                            <h4 class="font-semibold mb-1 text-primary-600">
                                Резюме:
                            </h4>
                        </div>
                    </div>
                </div>
            </UCard>
        </template>
    </div>
</template>

<script setup lang="ts">
const config = useRuntimeConfig();
const apiBaseUrl = config.public.apiBase;

const resume = ref<any | null>(null);
const error = ref<string | null>(null);
const avatarUrl = ref<string>('/default-avatar.png');
const avatarError = ref(false);

// Resume model computed from mock data
const resumeModel = computed(() => {
    if (error.value) {
        return { loading: false, error: error.value, data: null };
    }
    if (!resume.value) {
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
        resume.value = data || null;

        await fetchAvatar();
    } catch (err) {
        console.error('Error fetching resume:', err);
        error.value = 'Failed to load resume';
    }
});

// Fetch avatar for mock resume
async function fetchAvatar() {
    avatarError.value = false;

    if (!resumeModel.value.data?.photo) {
        return;
    }

    try {
        const domain = new URL(apiBaseUrl).origin;
        const photoEndpoint = resumeModel.value.data.photo;
        const avatarResponse = await fetch(`${domain}${photoEndpoint}`);

        if (!avatarResponse.ok) throw new Error('Avatar not found');

        const blob = await avatarResponse.blob();
        avatarUrl.value = URL.createObjectURL(blob);
    } catch (err) {
        console.error('Error fetching avatar:', err);
        avatarError.value = true;
    }
}

// Utilities
function extractCitizenship(text: unknown): string {
    if (typeof text !== 'string') return '-ошибка-';

    const match = text.match(/Гражданство: (.+)/);
    return match ? match[1] : '—';
}

function extractAbout(text: unknown): string {
    if (typeof text !== 'string') return '-ошибка-';

    const match = text.match(
        /Подробнее о навыках:([\s\S]*?)(?:\r?\n[A-ZА-ЯЁ][^:\r\n]+:|$)/
    );
    return match ? match[1].trim() : '';
}

function parseResponseDate(dateStr: unknown): string {
    if (typeof dateStr !== 'string') return '-ошибка-';

    const date = new Date(dateStr);
    if (isNaN(date.getTime())) return '-неверный формат-';

    const day = date.getDate().toString().padStart(2, '0');
    const month = (date.getMonth() + 1).toString().padStart(2, '0');
    const year = date.getFullYear();

    return `${day}.${month}.${year}`;
}
</script>
