<script setup lang="ts">
import { Button } from "primevue";
import { ref } from "vue";

interface Test<T> {
  id: number;
  quiz_title: string;
  questions: Question<T>[];
}

interface Question<T> {
  id: number;
  answers: T[];
  text: string;
  source: string;
  correct_answer_index: number;
  chosen_anwser_index?: number;
}

type TestPretransform = Omit<Test<any>, "id">;

const files = import.meta.glob("/data/*.json", {
  eager: true,
  import: "default",
});

const tests_pre_transform = [
  ...Object.values(files).map((file) => file as TestPretransform),
];

const transformTest = (test: TestPretransform) => {
  test.questions = test.questions
    .map((q) => ({ ...q, rng: Math.random() }))
    .sort((a, b) => a.rng - b.rng);
};

const BASE_TESTS: Test<string | boolean>[] = [];
for (const test of tests_pre_transform) {
  transformTest(test);
  BASE_TESTS.push({ ...test, id: BASE_TESTS.length });
}

const tests = ref<Test<string | boolean>[]>(BASE_TESTS);

const checkCorrectAnswer = (
  answer: number,
  question: Question<string | boolean>,
  test_id: number,
) => {
  const test = tests.value.find((t) => t.id === test_id)!;
  const questions = test.questions.map((q) =>
    q.id === question.id
      ? ({ ...q, chosen_anwser_index: answer } as Question<string | boolean>)
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
    <div v-for="test in tests" :key="test.id" class="max-w-[80%]">
      <div v-for="question in test.questions" :key="question.id" class="mb-16">
        <p class="text-2xl border rounded-2xl p-5 mb-4">
          {{ question.text }}
        </p>
        <div class="flex flex-wrap md:flex-nowrap justify-center gap-4 mb-4">
          <Button
            v-for="(answer, answer_idx) in question.answers"
            :key="answer_idx"
            class="w-80 h-40"
            :label="answer.toString()"
            @click="() => checkCorrectAnswer(answer_idx, question, test.id)"
            :pt="{
              label: { class: 'text-white text-2xl' },
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
        <p
          v-if="question.chosen_anwser_index !== undefined"
          class="text-2xl italic border border-blue-500 rounded-2xl p-5 mb-4"
        >
          <u>Πηγή</u>: {{ question.source }}
        </p>
      </div>
    </div>
  </div>
</template>
