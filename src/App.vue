<template>
  <main class="min-h-screen bg-slate-900">
    <div
      v-if="isOpen"
      class="absolute h-full w-full z-50 flex items-center justify-center bg-slate-600/40"
    >
      <div class="bg-blue-400 p-8 rounded-md relative flex flex-col w-[800px]">
        <textarea
          name=""
          id=""
          cols="30"
          rows="10"
          class="p-2"
          v-model.trim="newNotes"
        ></textarea>
        <p v-if="errorMsg" class="text-sm text-red-600">{{ errorMsg }}</p>
        <button
          @click.prevent="addNote"
          class="bg-blue-700 rounded-md uppercase text-white py-2 font-bold mt-4"
        >
          Add Note
        </button>

        <button
          @click.prevent="openCloseModal"
          class="absolute bg-red-700 rounded-full h-5 w-5 flex items-center justify-center top-2 right-2"
        >
          x
        </button>
      </div>
    </div>
    <div class="container p-8">
      <nav class="flex items-center justify-between text-white">
        <h1 class="md:text-3xl text-sm font-bold">MyNotes</h1>
        <button
          @click.prevent="openCloseModal"
          class="bg-blue-700 px-4 py-2 rounded-full font-semibold uppercase text-sm"
        >
          Add Note
        </button>
      </nav>

      <ul class="flex mt-10 gap-4 flex-wrap md:flex-row flex-col items-center">
        <div
          class="mx-auto text-sm text-white text-center md:text-4xl"
          v-if="noteMsg"
        >
          Click "Add Note" to begin, and click on a note to delete it.
        </div>
        <li
          @click.prevent="deleteNote"
          class="flex flex-col justify-between p-2 w-56 h-56 rounded-md"
          v-for="(note, index) in noteArr"
          :key="index"
          :style="{ backgroundColor: note.bgColor }"
        >
          <p>{{ note.text }}</p>
          <p class="text-xs font-bold">{{ note.date }}</p>
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { onMounted, ref } from "vue";

const isOpen = ref(false);
const noteMsg = ref(true);

const errorMsg = ref("");

const newNotes = ref("");
const noteArr = ref([]);

const getRandomColor = () => {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
};

const addNote = () => {
  if (newNotes.value.length < 10) {
    return (errorMsg.value = "Note needs 10 or more characters");
  } else {
    noteArr.value.push({
      text: newNotes.value,
      date: new Date().toLocaleDateString("en-us"),
      bgColor: getRandomColor(),
    });
    errorMsg.value = "";
    newNotes.value = "";
    isOpen.value = false;
    noteMsg.value = noteArr.value.length === 0;

    localStorage.setItem("noteArr", JSON.stringify(noteArr.value));
  }
};

onMounted(() => {
  const storedNotes = localStorage.getItem("noteArr");

  if (storedNotes) {
    noteArr.value = JSON.parse(storedNotes);
    noteMsg.value = noteArr.value.length === 0;
  }
});

const deleteNote = (index) => {
  noteArr.value.splice(index, 1);
  localStorage.setItem("noteArr", JSON.stringify(noteArr.value));
  noteMsg.value = noteArr.value.length === 0;
};

const openCloseModal = () => {
  isOpen.value = !isOpen.value;
  errorMsg.value = "";
  newNotes.value = "";
};
</script>
