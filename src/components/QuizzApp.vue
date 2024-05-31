<template>
  <div>
    <div
      :class="isSideMenu ? 'w-[calc(100%-45%)]' : 'w-[calc(100%-65px)]'"
      class="shadow-md duration-300"
    >
      <h1 class="py-8 text-3xl text-center">Matematika</h1>
    </div>

    <div
      class="m-10 duration-300"
      :class="isSideMenu ? 'w-[calc(100%-50%)] ' : 'w-[1000px] mx-auto '"
    >
      <QuizzItem
        v-for="(quizz, index) in quizzes"
        :quizz="quizz"
        @user-select-answer="handleSelectAnswer"
      />
    </div>

    <div
      :class="isSideMenu ? 'w-[calc(100%-45%)]' : 'w-[calc(100%-65px)]'"
      class="bg-black flex duration-300 py-40 justify-center items-center flex-wrap gap-6"
    >
      <div v-if="isFinishTest" class="text-white w-full">
      <!-- <div class="text-white w-full"> -->
        <h1 class="text-center">To'g'ri javoblar soni : {{ trueQuizzes }}</h1>
        <h1 class="text-center">Umumiy foiz : {{ percentQuizzes }} %</h1>
        <h1 class="text-center">Umumiy ball : {{ userBall }} / 100</h1>

        <circle-progress :percent="percentQuizzes" class="mx-auto mt-6" />
      </div>

      <button
        @click="finishTest"
        class="py-3 px-6 rounded bg-[#3cafb3] text-xl text-white"
      >
        {{ isFinishTest ? "Qayta topshirish" : "Testni yakunlash" }}
      </button>
    </div>

    <QuizzSider
      :quizzes="quizzes"
      v-model="isSideMenu"
      @finish:test="finishTestExit"
    />
  </div>
</template>

<script setup>
import "vue3-circle-progress/dist/circle-progress.css";
import CircleProgress from "vue3-circle-progress";
import { ref, reactive } from "vue";
import QuizzSider from "./QuizzSider.vue";
import QuizzItem from "./QuizzItem.vue";

const isSideMenu = ref(false);

const quizzes = reactive([
  {
    id: 1,
    question: "2+2 = ?",
    answers: ["4", "2", "3", "5"],
    correctAnswer: "4",
  },
  {
    id: 2,
    question: "2+12 = ?",
    answers: ["4", "2", "3", "5"],
    correctAnswer: "4",
  },
  {
    id: 3,
    question: "2+6 = ?",
    answers: ["4", "2", "3", "5"],
    correctAnswer: "4",
  },
  {
    id: 4,
    question: "5+2 = ?",
    answers: ["4", "2", "3", "5"],
    correctAnswer: "4",
    // userAnswer: "3"
  },
  {
    id: 5,
    question: "1+2 = ?",
    answers: ["4", "2", "3", "5"],
    correctAnswer: "4",
  },
]);

const handleSelectAnswer = (obj) => {
  quizzes.forEach((item) => {
    if (item.id == obj.id) {
      item.userAnswer = obj.userAnswer;
    }
  });
};

const isFinishTest = ref(false);
const trueQuizzes = ref(0);
const percentQuizzes = ref(0);
const userBall = ref(0);

const finishTestExit = () => {
  resetState();
};

const finishTest = () => {
  isFinishTest.value = true;
  quizzes.forEach((item) => {
    if (item.correctAnswer == item.userAnswer) {
      trueQuizzes.value++;
    }
  });

  percentQuizzes.value = (trueQuizzes.value * 100) / quizzes.length;
  userBall.value = percentQuizzes.value;
};
</script>

<style lang="scss" scoped></style>
