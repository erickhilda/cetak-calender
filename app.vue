<script setup lang="ts">
import { ref } from "vue";
import Calendar from "./components/calendar.vue"; // Assuming Calendar.vue exists

const backgrounds = [
  "https://images.unsplash.com/photo-1490750967868-88aa4486c946?auto=format&fit=crop&w=1920&q=80",
  "https://images.unsplash.com/photo-1508615070457-7baeba4003ab?auto=format&fit=crop&w=1920&q=80",
  "https://images.unsplash.com/photo-1496062031456-07b8f162a322?auto=format&fit=crop&w=1920&q=80",
  "https://images.unsplash.com/photo-1502977249166-824b3a8a4d6d?auto=format&fit=crop&w=1920&q=80",
];

const year = ref(2025);
const currentMonth = ref(new Date().getMonth());
const currentBackground = ref(0);

const handlePrint = () => {
  window.print();
};

const nextMonth = () => {
  if (currentMonth.value === 11) {
    currentMonth.value = 0;
    year.value++;
  } else {
    currentMonth.value++;
  }
};

const prevMonth = () => {
  if (currentMonth.value === 0) {
    currentMonth.value = 11;
    year.value--;
  } else {
    currentMonth.value--;
  }
};

const changeBackground = () => {
  currentBackground.value = (currentBackground.value + 1) % backgrounds.length;
};

const pageTitle = "Kalender Interaktif | Cetak Kalender Bulanan dan Tahunan";
const metaDescription =
  "Lihat dan cetak kalender bulanan dan tahunan dengan mudah. Tampilkan tanggal penting dan hari libur. Tersedia fitur cetak kalender langsung dari browser.";
const metaKeywords =
  "kalender, kalender bulanan, kalender tahunan, cetak kalender, tanggal, hari libur, perencanaan, jadwal";

useHead({
  title: pageTitle,
  meta: [
    { name: "description", content: metaDescription },
    { name: "keywords", content: metaKeywords },
    { property: "og:title", content: pageTitle },
    { property: "og:description", content: metaDescription },
    { property: "og:type", content: "website" },
    {
      property: "og:url",
      content: "https://cetak kalender.vercel.app",
    },
    { name: "twitter:card", content: "summary_large_image" },
    { name: "twitter:title", content: pageTitle },
    { name: "twitter:description", content: metaDescription },
  ],
  link: [{ rel: "icon", type: "image/x-icon", href: "/favicon.ico" }],
});
</script>

<template>
  <div class="min-h-screen bg-gray-100">
    <div class="print:hidden bg-white border-b">
      <div class="max-w-7xl mx-auto px-4 py-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center">
          <div class="flex items-center gap-6">
            <h1 class="text-2xl text-gray-900">Calendar</h1>
            <USelect
              v-model="year"
              :items="Array.from({ length: 10 }, (_, i) => year - 5 + i)"
            />
          </div>
          <div class="flex items-center gap-4">
            <UButton variant="subtle" @click="changeBackground">
              Change Background
            </UButton>
            <div class="flex items-center gap-2">
              <UButton
                icon="i-lucide-chevron-left"
                variant="subtle"
                size="lg"
                @click="prevMonth"
              />

              <UButton
                icon="i-lucide-chevron-right"
                variant="subtle"
                size="lg"
                @click="nextMonth"
              />
            </div>
            <UButton
              icon="i-lucide-printer"
              variant="subtle"
              size="lg"
              @click="handlePrint"
            />
          </div>
        </div>
      </div>
    </div>

    <div class="max-w-7xl mx-auto px-4 py-8 sm:px-6 lg:px-8">
      <div class="w-full max-w-4xl mx-auto">
        <Calendar
          :year="year"
          :month="currentMonth"
          :background="backgrounds[currentBackground]"
        />
      </div>
    </div>
  </div>
</template>
