<script setup lang="ts">
import { Button } from "primevue";

interface Question {
  answers: string[];
  text: string;
  correct_answer_index: number;
}

const questions: Question[] = [
  {
    text: "who is the best?",
    answers: ["nikos", "maria", "dimitra"],
    correct_answer_index: 1,
  },
  {
    text: "who is the worst?",
    answers: ["asdklfjklasd", "akllk", "kat"],
    correct_answer_index: 0,
  },
];

const checkCorrectAnswer = (answer: number, question: Question) => {
  console.log(question.correct_answer_index == answer);
};
</script>

<template>
  <div class="flex flex-col gap-8 min-w-dvw min-h-dvh items-center py-5">
    <h1 class="text-6xl">Practical Quiz</h1>
    <div v-for="question in questions">
      <p class="text-3xl border rounded-2xl p-5 mb-4">{{ question.text }}</p>
      <div class="flex gap-3">
        <Button
          v-for="(answer, answer_idx) in question.answers"
          class="w-80 h-40 mb-8"
          :label="answer"
          @click="() => checkCorrectAnswer(answer_idx, question)"
          :pt="{ label: { class: 'text-3xl' } }"
        />
      </div>
    </div>
  </div>
</template>
