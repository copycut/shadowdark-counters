<script setup>
import { ref, computed } from 'vue';

const DAY_START = 7;
const DAY_END = 19;
const DAY_NIGHT_MULTIPLIER = 6;

const dayNight = ref(new Date());

dayNight.value.setHours(DAY_START);
dayNight.value.setMinutes(0);
dayNight.value.setSeconds(0);

const dayNightIsStopped = ref(true);
const dayNightInterval = ref(null);

function startDayNight() {
  dayNightIsStopped.value = false;
  clearInterval(dayNightInterval.value);

  dayNightInterval.value = setInterval(() => {
    if (dayNightIsStopped.value) return;

    dayNight.value = new Date(dayNight.value.setSeconds(dayNight.value.getSeconds() + 1));
  }, 1000 / DAY_NIGHT_MULTIPLIER);
}

function stopDayNight() {
  dayNightIsStopped.value = true;
}

function clearDayNight() {
  clearInterval(dayNightInterval.value);
  dayNight.value = new Date();
  dayNight.value.setHours(DAY_START);
  dayNight.value.setMinutes(0);
  dayNight.value.setSeconds(0);
}

const isSunrise = computed(() => {
  const hours = dayNight.value.getHours();
  return hours >= DAY_START && hours < DAY_END;
});
</script>

<template>
  <Card>
    <template #title>Durée du jour</template>
    <template #content>
      <div class="time-display">
        <span class="sun-moon-icon">
          <i v-if="isSunrise" :class="['pi pi-sun', { 'pi-spin': !dayNightIsStopped }]" />
          <i v-else class="pi pi-moon" />
        </span>
        <span> {{ dayNight.toLocaleTimeString('fr-FR') }}</span>
      </div>
      <DatePicker v-model="dayNight" :disabled="!dayNightIsStopped" timeOnly fluid class="time-keeper" />
      <Button :label="dayNightIsStopped ? 'Commencer' : 'En cours'" icon="pi pi-play" @click="startDayNight" />
      <Button severity="warn" icon="pi pi-stop" @click="stopDayNight" />
      <Button severity="danger" icon="pi pi-times" @click="clearDayNight" />
    </template>
  </Card>
</template>

<style scoped>
.time-display {
  display: flex;
  align-items: center;
  justify-content: center;
  padding-bottom: 0.5rem;
}

.time-keeper {
  margin-bottom: 0.5rem;
}

.sun-moon-icon {
  margin-right: 0.5rem;
}
</style>
