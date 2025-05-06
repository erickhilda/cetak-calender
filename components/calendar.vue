<script setup lang="ts">
interface Holiday {
  holiday_date: string;
  holiday_name: string;
  is_national_holiday: boolean;
}

interface CalendarProps {
  year: number;
  month: number;
  background: string;
}

const props = defineProps<CalendarProps>();

const monthNames = [
  "January",
  "February",
  "March",
  "April",
  "May",
  "June",
  "July",
  "August",
  "September",
  "October",
  "November",
  "December",
];

const currentMonth = computed(() => props.month + 1);
const currentYear = computed(() => props.year);
const { data: holidays } = await useFetch<Holiday[]>(
  "https://api-harilibur.vercel.app/api",
  {
    query: { month: currentMonth, year: currentYear },
    watch: [currentMonth, currentYear],
  },
);

const getDaysInMonth = (year: number, month: number) => {
  return new Date(year, month + 1, 0).getDate();
};

const getFirstDayOfMonth = (year: number, month: number) => {
  return new Date(year, month, 1).getDay();
};

function getDaysOfNextMonthToDisplay(year: number, month: number) {
  const daysInMonth = getDaysInMonth(year, month);
  const lastDayOfMonth = new Date(year, month, daysInMonth).getDay(); // Weekday of last day
  return lastDayOfMonth === 6 ? 0 : 6 - lastDayOfMonth;
}

const daysInMonth = computed(() => getDaysInMonth(props.year, props.month));
const firstDayOfMonth = computed(() =>
  getFirstDayOfMonth(props.year, props.month),
);
const blanksPrev = computed(() =>
  Array.from({ length: firstDayOfMonth.value }, (_, i) => i),
);
const daysOfNextMonthToDisplay = computed(() =>
  getDaysOfNextMonthToDisplay(props.year, props.month),
);
const blanksNext = computed(() =>
  Array.from({ length: daysOfNextMonthToDisplay.value }, (_, i) => i),
);
const days = computed(() =>
  Array.from({ length: daysInMonth.value }, (_, i) => i + 1),
);

function isWeekend(year: number, month: number, day: number): boolean {
  const date = new Date(year, month, day);
  const dayOfWeek = date.getDay(); // 0 (Sunday) to 6 (Saturday)
  return dayOfWeek === 0 || dayOfWeek === 6;
}

const isHoliday = (day: number) => {
  const dateStr = `${props.year}-${String(props.month + 1).padStart(2, "0")}-${Number(day)}`;
  if (!holidays.value) {
    return {
      holiday_date: "",
      holiday_name: "",
      is_national_holiday: false,
    };
  }
  return holidays.value.find((h) => h.holiday_date === dateStr);
};
</script>

<template>
  <div
    class="bg-white rounded-xl shadow-xl overflow-hidden print:shadow-none print:border transition-all duration-500"
    :style="{
      backgroundImage: `url('${props.background}')`,
      backgroundSize: 'cover',
      backgroundPosition: 'center',
    }"
  >
    <div class="bg-white/40 backdrop-blur-xs">
      <div
        class="px-8 py-6 flex justify-between text-4xl font-ui-serif text-gray-900 tracking-wide"
      >
        <h2>{{ props.year }}</h2>
        <h2>{{ monthNames[props.month] }}</h2>
      </div>

      <div class="px-8 pb-8">
        <div class="grid grid-cols-7 gap-px rounded-lg overflow-hidden">
          <div
            v-for="day in ['Min', 'Sen', 'Sel', 'Rab', 'Kam', 'Jum', 'Sab']"
            :key="day"
            class="bg-gray-300/60 backdrop-blur-sm py-3 text-sm font-ui-serif text-center"
            :class="{
              'text-red-600': ['Sun', 'Sat'].includes(day),
              'text-gray-500': !['Sun', 'Sat'].includes(day),
            }"
          >
            {{ day }}
          </div>

          <div
            v-for="blank in blanksPrev"
            :key="`blank-prev-${blank}`"
            class="aspect-square backdrop-blur-sm bg-white/20"
          />

          <div
            v-for="day in days"
            :key="day"
            class="relative bg-white/60 backdrop-blur-sm aspect-square group transition-all duration-200 hover:bg-white/80"
            :class="{
              'bg-red-200/60': isHoliday(day)?.is_national_holiday,
            }"
          >
            <div class="absolute inset-1 flex flex-col">
              <span
                class="text-lg leading-none p-2 font-ui-serif"
                :class="{
                  'text-red-600':
                    isHoliday(day)?.is_national_holiday ||
                    isWeekend(props.year, props.month, day),
                  'text-gray-700':
                    !isHoliday(day)?.is_national_holiday &&
                    !isWeekend(props.year, props.month, day),
                }"
              >
                {{ day }}
              </span>

              <div v-if="isHoliday(day)" class="px-2 mt-1">
                <div
                  class="text-xs font-ui-serif font-medium truncate"
                  :class="{
                    'text-red-600':
                      isHoliday(day)?.is_national_holiday ||
                      isWeekend(props.year, props.month, day),
                    'text-gray-600':
                      !isHoliday(day)?.is_national_holiday &&
                      !isWeekend(props.year, props.month, day),
                  }"
                >
                  {{ isHoliday(day)?.holiday_name }}
                </div>
              </div>
            </div>
          </div>

          <div
            v-for="blank in blanksNext"
            :key="`blank-next-${blank}`"
            class="aspect-square backdrop-blur-sm bg-white/20"
          />
        </div>
      </div>

      <div
        class="px-8 py-6 bg-gray-50/75 backdrop-blur-sm border-t border-gray-200"
      >
        <h3 class="text-lg font-ui-serif font-medium text-gray-900 mb-4">
          Hari Penting dan Hari Besar Keagamaan
        </h3>
        <div class="space-y-2 text-xs grid grid-cols-3">
          <template v-if="holidays && holidays.length > 0">
            <div
              v-for="holiday in holidays"
              :key="holiday.holiday_date"
              class="flex items-start justify-items-start gap-3 font-ui-serif"
              :class="{
                'text-red-600': holiday.is_national_holiday,
                'text-gray-600': !holiday.is_national_holiday,
              }"
            >
              <span class="font-medium">{{
                holiday.holiday_date.split("-")[2]
              }}</span>
              <span class="flex-1">{{ holiday.holiday_name }}</span>
            </div>
          </template>
          <p v-else class="text-gray-500 italic font-ui-serif">
            Tidak terdapat hari
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Scoped styles can be added here */
</style>
