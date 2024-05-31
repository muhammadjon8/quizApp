<script setup>
import { toRefs, ref, watch, computed, onMounted, onBeforeUnmount } from "vue";
import { Icon } from "@iconify/vue";

const props = defineProps({
  modelValue: Boolean,
  quizzes: {
    type: Array,
    required: true,
  },
});

const { modelValue } = toRefs(props);

const count = ref(0);
const initialTime = 360; 
const remainingTime = ref(initialTime); 
let countdownInterval = null;

const emit = defineEmits(["update:modelValue", "finish:test"]);

const toogleSide = () => {
  emit("update:modelValue", !modelValue.value);
};

const resetQuizzes = () => {
  props.quizzes.forEach(quiz => {
    quiz.userAnswer = null; 
  });
};

const finish = () => {
  clearInterval(countdownInterval);
  toogleSide();
  emit("finish:test");
  reset(); 
};

const reset = () => {
  remainingTime.value = initialTime;
  resetQuizzes();
  count.value = 0;
  startCountdown(); 
};

const updateCount = () => {
  count.value = props.quizzes.reduce(
    (acc, quiz) => acc + (quiz.userAnswer ? 1 : 0),
    0
  );
};

watch(props.quizzes, updateCount, { deep: true });

const buttonClasses = computed(() => {
  return props.quizzes.map((quiz) => {
    return quiz.userAnswer ? "bg-black text-white" : "";
  });
});

const startCountdown = () => {
  clearInterval(countdownInterval); 
  countdownInterval = setInterval(() => {
    if (remainingTime.value > 0) {
      remainingTime.value--;
    } else {
      clearInterval(countdownInterval);
      finish(); 
    }
  }, 1000);
};


const formatTime = (timeInSeconds) => {
  const hours = Math.floor(timeInSeconds / 3600);
  const minutes = Math.floor((timeInSeconds % 3600) / 60);
  const seconds = timeInSeconds % 60;
  return `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
};

const formattedTime = computed(() => formatTime(remainingTime.value));

onMounted(() => {
  startCountdown();
});

onBeforeUnmount(() => {
  clearInterval(countdownInterval);
});
</script>

<template>
  <div
    :class="modelValue ? 'w-[45%]' : 'w-[65px]'"
    class="fixed top-0 right-0 h-screen duration-300 bg-primary"
  >
    <div class="flex items-center justify-between shadow-md pr-5">
      <div>
        <button
          class="bg-orange-700 py-8 px-2 flex justify-center hover:bg-slate-900 hover:text-white"
          :class="!modelValue ? 'w-[65px]' : ''"
          @click="toogleSide"
        >
          <Icon
            :class="!modelValue ? '' : 'rotate-180'"
            class="text-4xl"
            icon="iconamoon:arrow-left-2-bold"
          />
        </button>
      </div>

      <div class="text-3xl font-medium py-8">{{ formattedTime }}</div>

      <button @click="finish" class="text-2xl text-[#ccc] py-8">
        chiqish ->
      </button>
    </div>
    <div :class="!modelValue ? 'hidden' : ''">
      <div class="flex text-xl font-medium justify-between px-6 py-2">
        <h2>Matematika</h2>
        <h2>{{ count }}/10</h2>
      </div>
      <div class="flex gap-8 p-6">
        <button
          v-for="(item, index) in props.quizzes"
          :key="index"
          :class="['border border-[#280a0a] flex items-center justify-center w-[40px] h-[40px] rounded', buttonClasses[index]]"
        >
          {{ index + 1 }}
        </button>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
