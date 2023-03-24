<template>
  <div>
    <textarea
      name="nt-title"
      id="nt-title"
      cols="30"
      rows="10"
      v-model="title"
      @keydown.tab="createTask"
      @keydown.enter="createTask"
      class="focus:bg-white focus:shadow resize-none rounded w-full border-none"
      :class="{
        'h-7': !focused,
        'h-20': focused,
      }"
      style="outline: none !important"
      @focus="focused = true"
      @blur="focused = false"
      :placeholder="!focused ? '+ Add a Card ' : 'Enter a title for this card'"
    ></textarea>
  </div>
</template>

<script setup lang="ts">
import { Column, Task } from "../types";
import { nanoid } from "nanoid";

const emit = defineEmits<{
  (e: "add", payload: Task): void;
}>();

const focused = ref(false);
const title = ref("");

const createTask = (e: Event) => {
  if (title.value.trim()) {
    e.preventDefault();
    emit("add", {
      title: title.value.trim(),
      createdAt: new Date(),
      id: nanoid(),
    } as Task);
  }

  title.value = "";
};
</script>
