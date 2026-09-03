<script setup lang="ts">
import { Button } from "primevue";
import data_test from "../data.json";
import data_true_or_false from "../true_or_false.json";
import { ref } from "vue";

interface Test {
  id: number;
  quiz_title: string;
  questions: Question[];
}

interface Question {
  id: number;
  answers: string[];
  text: string;
  correct_answer_index: number;
  chosen_anwser_index?: number;
}

data_test.questions = data_test.questions
  .map((q) => ({ ...q, rng: Math.random() }))
  .sort((a, b) => a.rng - b.rng);

data_true_or_false.questions = data_true_or_false.questions
  .map((q) => ({ ...q, rng: Math.random() }))
  .sort((a, b) => a.rng - b.rng);

const tests = ref<Test[]>([
  { ...data_test, id: 0 },
  { ...data_true_or_false, id: 1 },
]);

const checkCorrectAnswer = (
  answer: number,
  question: Question,
  test_id: number,
) => {
  const test = tests.value.find((t) => t.id === test_id)!;
  const questions = test.questions.map((q) =>
    q.id === question.id
      ? ({ ...q, chosen_anwser_index: answer } as Question)
      : q,
  );

  tests.value = tests.value.map((t) =>
    t.id === test_id ? { ...t, questions } : t,
  );
};
</script>

<template>
  <div class="flex flex-col gap-8 min-w-dvw min-h-dvh items-center py-5">
    <h1 class="text-6xl">Practical Quiz</h1>
    <div v-for="test in tests" :key="test.id">
      <div class="max-w-[80%]">
        <div v-for="question in test.questions" :key="question.id">
          <p class="text-2xl border rounded-2xl p-5 mb-4">
            {{ question.text }}
          </p>
          <div class="flex flex-wrap md:flex-nowrap justify-around gap-4 mb-16">
            <Button
              v-for="(answer, answer_idx) in question.answers"
              :key="answer_idx"
              class="w-80 h-40"
              :label="answer"
              @click="() => checkCorrectAnswer(answer_idx, question, test.id)"
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
  </div>
</template>
