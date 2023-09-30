<template>
  <div class="modal-container" :class="{ 'active': isActive }">
    <div class="overlay" @click="closeModal"></div>
    <div class="modal">
      <textarea v-model="query"></textarea>
      <div class="modal-btn-group">
        <div class="add-note-btn" v-if="addBtnState == false" @click="() => addNote()">Add Note</div>
        
        <div class="change-note-wrapper" v-else>
          <div class="delete-note-btn" @click="() => deleteNote()">Delete Note</div>
          <div class="save-note-btn" @click="() => changeNote()">Change Note</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

let isActive = ref(false)
let query = ref('');
const props = defineProps({
  addBtnState: {
    type: Boolean,
    default: false
  }
})


const openModal = () => {
  isActive.value = true;
  query.value = '';
}

const closeModal = () => {
  isActive.value = false;
}

const setNote = (context, payload) => {
  let notes = {};
  let key = context.state.noteItem.noteKey;


  if (localStorage.getItem('notes')) {
    notes = JSON.parse(localStorage.getItem('notes'));
  }

  if (!notes[key]) notes[key] = [];

  notes[key].push({
    text: payload,
    key: key,

  })

  localStorage.setItem('notes', JSON.stringify(notes));
  context.dispatch('getNotes');

}

const getNotes = (context) => {
  let notes = {};
  let calendarMonth = context.state.calendar;

  if (!!localStorage.getItem('notes')) {
    notes = JSON.parse(localStorage.getItem('notes'));
  } else {
    notes = [];
  }

  for (let i = 0; i < calendarMonth.length; i++) {
    calendarMonth[i].notes = notes[calendarMonth[i].noteKey];
  }
}

const deleteNote = (context) => {

  let deleteNote = context.state.selectedNote;
  let notes = {};

  if (localStorage.getItem('notes')) {
    notes = JSON.parse(localStorage.getItem('notes'));
  }

  notes[deleteNote.note.key].splice(deleteNote.index, 1);

  if (notes[deleteNote.note.key].length < 1) delete notes[deleteNote.note.key];

  localStorage.setItem('notes', JSON.stringify(notes));

  context.dispatch('getNotes');
}

const addNote = () => {
  closeModal();
  alert("Adding notes is in progress")
}

defineExpose({
  openModal,
  closeModal,
  setNote,
  getNotes
})


const changeNote = () => {
  closeModal();
}


</script>

<style>
.modal-container {
  position: fixed;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  overflow: hidden;
  opacity: 0;
  visibility: hidden;
  transition: all 0.5s ease;
  z-index: 5;
}

.modal-container.active {
  opacity: 1;
  visibility: visible;
}

.modal-container .overlay {
  position: fixed;
  background-color: rgba(0, 0, 0, 0.6);
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  z-index: 15;
}

.modal-container .modal {
  position: relative;
  z-index: 20;
  margin: 134px auto;
  width: 800px;
  background-color: #fff;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.modal-container .modal textarea {
  height: 207px;
  width: 100%;
  font-size: 18px;
  resize: none;
  outline: none;
  border: none;
}

.modal-container .modal .modal-btn-group {
  width: 100%;
}

.modal-container .modal .modal-btn-group .add-note-btn {
  padding: 15px 0;
  text-align: center;
  border: none;
  width: 100%;
  cursor: pointer;
  background-color: #2ED573;
  font-style: normal;
  font-weight: 500;
  font-size: 18px;
  line-height: 21px;
  letter-spacing: 0.05em;
}

.modal-container .modal .modal-btn-group .change-note-wrapper {
  display: flex;
  justify-content: space-between;
}

.modal-container .modal .modal-btn-group .change-note-wrapper .save-note-btn {
  width: 50%;
  padding: 15px 0;
  background-color: #2ED573;
  font-style: normal;
  font-weight: 500;
  font-size: 18px;
  line-height: 21px;
  letter-spacing: 0.05em;
  text-align: center;
  cursor: pointer;
}

.modal-container .modal .modal-btn-group .change-note-wrapper .delete-note-btn {
  width: 50%;
  padding: 15px 0;
  background-color: #FF5463;
  font-style: normal;
  font-weight: 500;
  font-size: 18px;
  line-height: 21px;
  letter-spacing: 0.05em;
  text-align: center;
  cursor: pointer;
}
</style>