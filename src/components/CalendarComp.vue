<template>
    <div class="calendar">
        <div class="calendar__locale-picker">
            <template v-for="locale in locales" :key="locale">
                <div class="calendar__locale">
                    <label :for="locale.name">
                        <input
                            :id="locale.name"
                            @click="changeLocale"
                            type="radio"
                            class="calendar__locale-item"
                            name="locale"
                            :value="locale.name"
                            :checked="locale.name === currentLocale.name ? true : false" />
                        {{ locale.label }}
                    </label>
                </div>
            </template>
        </div>
        <div class="calendar__header">
            <button @click="changeMonth" id="prev-month" class="calendar__previous-month">&lt;</button>
            <span class="calendar__current-month">{{ monthFormatted }}</span>
            <button @click="changeMonth" id="next-month"  class="calendar__next-month">&gt;</button>
        </div>
        <div class="calendar__body">
            <div class="calendar__day-row">
                <span class="calendar__day calendar__day--week" v-for="day in currentLocale.dayNames" :key="day">{{ day }}</span>
            </div>
            <div class="calendar__date-grid">
                <span class="calendar__day calendar__day--last-month" v-for="day in firstDayIndex" :key="day">{{ day }}</span>
                <button @click="selectDate" class="calendar__day calendar__day--month" v-for="day in days" :key="day">{{ day }}</button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, toRef, watch } from 'vue';

const props = defineProps({
    date: Date,
});

const locales = [
    {name: "en-US", label: "EN", dayNames: ['Mo','Tu','We','Th','Fr','Sa','Su']},
    {name: "ru-RU", label: "RU", dayNames: ['Пн','Вт','Ср','Чт','Пт','Сб','Вс']},
    {name: "de-DE", label: "DE", dayNames: ['Mo','Di','Mi','Do','Fr','Sa','So']},
];

let currentLocale = ref(locales[0]);

let baseDate = props.date ? ref(props.date) : ref(new Date());
let date = ref(baseDate.value.getDate());
let month = ref(baseDate.value.getMonth());
let year = ref(baseDate.value.getFullYear());

let days = ref(new Date(year.value, month.value + 1, 0).getDate());
let firstDayIndex = ref(new Date(year.value, month.value, 1).getDay() + 6);

let monthFormatted = ref(baseDate.value.toLocaleDateString(currentLocale.value.name, {
    month: "long",
    year: "numeric"
}));

function selectDate(event) {
    let selectedDate = new Date(year.value, month.value, event.target.innerHTML).toLocaleDateString(currentLocale.value.name, {
        weekday: "long",
        day: "numeric",
        month: "long",
        year: "numeric"
    });

    if (!event.target.classList.contains('calendar__day--active')) {
        document.querySelector('.calendar__day--active')?.classList.remove('calendar__day--active');
        event.target.classList.add('calendar__day--active');
    }

    console.log(':: Selected date:', selectedDate);
}

function changeMonth(event) {
    if (event.target.id === "prev-month") {
        month.value = month.value - 1;
    } else if (event.target.id === "next-month") {
        month.value = month.value + 1;
    }

    if (month.value < 0) {
        month.value = 11;
        year.value = year.value - 1;
    } else if (month.value > 11) {
        month.value = 0;
        year.value = year.value + 1;
    }

    baseDate.value = new Date(year.value, month.value, 1);

    monthFormatted.value = baseDate.value.toLocaleDateString(currentLocale.value.name, {
        month: "long",
        year: "numeric"
    });
}

function changeLocale(event) {
    currentLocale.value = locales.find(locale => locale.name === event.target.value);
}

watch(month, () => {
    days = new Date(year.value, month.value + 1, 0).getDate();
    firstDayIndex = new Date(year.value, month.value, 1).getDay() + 6;
});

watch(currentLocale, () => {
    monthFormatted.value = baseDate.value.toLocaleDateString(currentLocale.value.name, {
        month: "long",
        year: "numeric"
    });
});
</script>

<style scoped>
.calendar {
    display: grid;
    grid-template-rows: auto auto auto;
    grid-template-columns: auto auto;
    width: 100%;

    .calendar__locale-picker {
        display: flex;
        flex-flow: row nowrap;
        justify-content: center;
        align-items: center;
        grid-column: 1/-1;

        > * {
            margin-right: 1.25rem;
        }
    }

    .calendar__header {
        display: flex;
        flex-flow: row nowrap;
        justify-content: space-between;

        grid-row: 2;
        grid-column: 1/-1;
    }

    .calendar__body {
        display: grid;
        grid-column: 1/-1;
        width: 100%;

        .calendar__day {
            border: var(--border-default);
            margin: auto;
            padding: 0.5rem;
        }

        .calendar__day--week {
            font-weight: 700;
        }

        .calendar__day--month {
            background: transparent;
            border: var(--border-default);
            cursor: pointer;
            font-weight: 400;
        }

        .calendar__day--active {
            border: var(--border-active);
        }

        .calendar__date-grid,
        .calendar__day-row {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            grid-column: 1/-1;
            width: 100%;
        }

        .calendar__date-grid {
            .calendar__day--last-month {
                opacity: 0;
            }

            > .calendar__day.calendar__day--last-month:nth-child(-n+7) {
                display: none;
            }
        }
    }
}
</style>
