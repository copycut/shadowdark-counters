<script setup>
import { ref, computed } from 'vue';

const timer = ref(3600);
const interval = ref(null);
const isStopped = ref(true);
const alertEnd = ref(false);

function startTimer() {
  isStopped.value = false;
  clearInterval(interval.value);

  interval.value = setInterval(() => {
    if (isStopped.value) return;

    if (timer.value <= 0) {
      clearInterval(interval.value);
      alertEnd.value = true;
      return;
    }

    timer.value--;
  }, 1000);
}

function stopTimer() {
  isStopped.value = true;
}

function clearTimer() {
  clearInterval(interval.value);
  timer.value = 3600;
}

function closeDialog() {
  alertEnd.value = false;
}

const hours = computed(() => {
  const hours = Math.floor(timer.value / 60 / 60);
  return String(hours).padStart(2, '0');
});
const minutes = computed(() => {
  const minutes = Math.floor(timer.value / 60) % 60;
  return String(minutes).padStart(2, '0');
});
const seconds = computed(() => {
  const seconds = timer.value % 60;
  return String(seconds).padStart(2, '0');
});
</script>

<template>
  <Card class="torch-timer">
    <template #title>
      Timer de la torche
      <Message severity="warn"> {{ hours }}:{{ minutes }}:{{ seconds }} </Message>
    </template>
    <template #content>
      <Button :label="isStopped ? 'Commencer' : 'En cours'" icon="pi pi-play" @click="startTimer" />
      <Button severity="warn" icon="pi pi-stop" @click="stopTimer" />
      <Button severity="danger" icon="pi pi-times" @click="clearTimer" />
    </template>
  </Card>

  <Dialog v-model:visible="alertEnd" header="La torche est éteinte !" modal>
    <div class="flex justify-end gap-2">
      <Button type="button" label="Ok" severity="secondary" @click="closeDialog" />
    </div>
  </Dialog>
</template>

<style scoped>
.torch-timer {
  margin-bottom: 1rem;
}
</style>
