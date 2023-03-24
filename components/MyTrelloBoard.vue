<template>
  <div class="flex items-start overflow-x-auto gap-4">
    <draggable
      v-model="columns"
      group="columns"
      item-key="id"
      :animation="150"
      handle=".drag-handle"
      class="flex gap-4 items-start"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column bg-gray-200 p-5 rounded min-w-[250px]">
          <header class="font-bold mb-4">
            <drag-handle />
            <input
              type="text"
              class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? (columns = columns.filter((c) => c.id !== column.id))
                  : null
              "
              v-model="column.title"
            />
          </header>

          <draggable
            v-model="column.tasks"
            :group="{ name: 'tasks', pull: alt ? 'clone' : true }"
            item-key="id"
            handle=".drag-handle"
            :animation="150"
          >
            <template #item="{ element: task }: { element: Task }">
              <div>
                <my-trello-board-task
                  :task="task"
                  @delete="column.tasks = column.tasks.filter((t) => t.id !== $event)"
                />
              </div>
            </template>
          </draggable>

          <footer>
            <!-- <button class="text-gray-500">+ Add a card</button> -->
            <new-task @add="column.tasks.push($event)" />
          </footer>
        </div>
      </template>
    </draggable>
    <button
      @click="createColumn"
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
    >
      + Add Another Column
    </button>
  </div>
</template>

<script setup lang="ts">
import { Column, Task } from "../types";
import { nanoid } from "nanoid";
import draggable from "vuedraggable";

const columns = useLocalStorage<Column[]>("myTrelloBoard", [
  {
    id: nanoid(),
    title: "Imagine sth interesting written here",
    tasks: [
      { id: nanoid(), title: "Premier devoir", createdAt: new Date() },
      { id: nanoid(), title: "Deuxième devoir", createdAt: new Date() },
      { id: nanoid(), title: "Troisième devoir", createdAt: new Date() },
    ],
  },
  {
    id: nanoid(),
    title: "Imagine sth interesting written here 2",
    tasks: [{ id: nanoid(), title: "First task", createdAt: new Date() }],
  },
  {
    id: nanoid(),
    title: "Imagine sth interesting written here 3",
    tasks: [{ id: nanoid(), title: "Erste hausaufgaben", createdAt: new Date() }],
  },
  {
    id: nanoid(),
    title: "Imagine sth interesting written here 4",
    tasks: [{ id: nanoid(), title: "Erste hausaufgaben", createdAt: new Date() }],
  },
  {
    id: nanoid(),
    title: "Imagine sth interesting written here 5",
    tasks: [
      {
        id: nanoid(),
        title:
          "Lorem Ipsum is simply dummy text of the printing and typesetting industry.",
        createdAt: new Date(),
      },
    ],
  },
  {
    id: nanoid(),
    title: "Imagine sth interesting written here 6",
    tasks: [{ id: nanoid(), title: "Erste hausaufgaben", createdAt: new Date() }],
  },
]);

const alt = useKeyModifier("Alt");

const createColumn = () => {
  const column: Column = {
    id: nanoid(),
    title: "",
    tasks: [],
  };
  columns.value.push(column);
  nextTick(() => {
    (document.querySelector(
      ".column:last-of-type .title-input"
    ) as HTMLInputElement).focus();
  });
};
</script>

<style lang="postcss" scoped>
.sortable-chosen {
  background: green;
}

.sortable-drag .task {
  transform: rotate(5deg);
}

.sortable-ghost .task {
  position: relative;
}
.sortable-ghost .task::after {
  content: "";
  @apply absolute top-0 bottom-0 left-0 right-0 bg-slate-300 rounded;
}
</style>
