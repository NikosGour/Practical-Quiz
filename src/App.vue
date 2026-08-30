<script setup lang="ts">
import { Button } from "primevue";
import data from "../data.json";
import { ref } from "vue";

interface Question {
  id: number;
  answers: string[];
  text: string;
  correct_answer_index: number;
  chosen_anwser_index?: number;
}

const questions = ref<Question[]>(
  data.questions
    .map((q) => ({ ...q, rng: Math.random() }))
    .sort((a, b) => a.rng - b.rng),
);

const checkCorrectAnswer = (answer: number, question: Question) => {
  questions.value = questions.value.map((q) =>
    q.id === question.id
      ? ({ ...q, chosen_anwser_index: answer } as Question)
      : q,
  );
};
</script>

<template>
  <div class="flex flex-col gap-8 min-w-dvw min-h-dvh items-center py-5">
    <h1 class="text-6xl">Practical Quiz</h1>
    <div class="max-w-[80%]">
      <div v-for="question in questions" :key="question.id">
        <p class="text-2xl border rounded-2xl p-5 mb-4">
          {{ question.text }}
        </p>
        <div class="flex flex-wrap justify-around gap-4 mb-16">
          <Button
            v-for="(answer, answer_idx) in question.answers"
            :key="answer_idx"
            class="w-80 h-40"
            :label="answer"
            @click="() => checkCorrectAnswer(answer_idx, question)"
            :pt="{
              label: { class: 'text-white text-2xl' },
              // root: { class: 'bg-stone-600! border-stone-400!' },
              root: {
                class:
                  question.chosen_anwser_index === undefined
                    ? 'bg-stone-600! border-stone-400!'
                    : answer_idx === question.correct_answer_index
                      ? 'bg-green-400! border-green-300!'
                      : answer_idx === question.chosen_anwser_index &&
                          question.chosen_anwser_index !==
                            question.correct_answer_index
                        ? 'bg-red-400! border-red-300!'
                        : 'bg-stone-600! border-stone-400!',
              },
            }"
          />
        </div>
      </div>
    </div>
  </div>
</template>
