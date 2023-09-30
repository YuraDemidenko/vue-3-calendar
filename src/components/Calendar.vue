<script setup>
import { ref, computed, onMounted } from 'vue'
import Modal from '../components/modal.vue'

const curentDate = ref(new Date())
const monthNames = ref(['January', 'February', 'March', 'April', 'May',
  'June', 'July', 'August', 'September', 'October', 'November', 'December'
])
let month = ref(0)
let year = ref(0)
let day = ref(0)
const text = ref()
const dayNote = ref('')
const changeNote = ref(false)
const timestamp = ref('')
const modal = ref(null)


const date = (year, month, day) => {
  return new Date(year, month, day);
}

const getDay = (year, month, day) => {
  let dayNumber = date(year, month, day).getDay();
  if (dayNumber === 0) dayNumber = 7;
  return dayNumber
}

const createCalendar = (year, month, day) => {
  const calendarMonth = [];
  let key = '';
  let pastMonth = ((date(year, month, 0).getDate()) - (getDay(year, month, day)) + 1);

  for (let i = 0; i < (getDay(year, month, day)); i++) {
    calendarMonth.push({
      day: pastMonth + i,
      month: month,
      year: year,
      date: year + '-' + month + '-' + (pastMonth + i),
      noteKey: key.concat(year, (month - 1), (pastMonth + i)),
      notes: {},

    });

  }

  for (let i = 0; i < date(year, (month + 1), (day = 0)).getDate(); i++) {
    calendarMonth.push({
      day: i + 1,
      month: getNow(curentDate).monthName,
      year: year,
      date: year + '-' + (month + 1) + '-' + (i + 1),
      notes: {},
      noteKey: key.concat(year, month, (i + 1)),
      currentDate: false
    });
  }

  for (let i = 0; i < (7 - getDay(year, (month + 1), (day = 0))); i++) {
    calendarMonth.push({
      day: i + 1,
      month: month + 2,
      year: year,
      date: year + '-' + (month + 2) + '-' + (i + 1),
      noteKey: key.concat(year, (month + 1), (i + 1)),
      notes: {},

    });
  }
  return calendarMonth;
}

const getNow = (now) => {
  let today = now.value;
  return {
    date: today.getFullYear() + '-' + (today.getMonth() + 1) + '-' + today.getDate(),
    monthName: monthNames.value[today.getMonth()],
    monthNumber: today.getMonth(),
    day: today.getDate(),
    today: today,
    year: today.getFullYear(),
    time: today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds(),
    dayNumber: today.getDay()
  }

}

const activeModal = (item, index) => {
  modal.value.openModal();
}

const closeModal = (item, index) => {
  modal.value.closeModal();
}

const previousMonth = () => {
  month.value--;
  if (month.value == -1) {
    month.value = 11;
    year.value--;
  }
  // this.marckToday();
  console.log(currentDay.value);
}

const nextMonth = () => {
  month.value++;
  if (month.value == 12) {
    month.value = 0;
    year.value++;
  }

  // this.marckToday();

}

const today = () => {
  year.value = currentDay.value.year;
  month.value = currentDay.value.monthNumber;
  marckToday();
}

const marckToday = () => {
  const currentDay = getNow(curentDate).date;
  for (let i = 0; i < calendar.value.length; i++) {
    if (calendar.value[i].date == currentDay) {
      calendar.value[i].currentDate = true;
    }
  }
}

const calendar = computed(() => {
  return createCalendar(year.value, month.value, day.value)
})

const currentDay = computed(() => {
  return getNow(curentDate);
})

onMounted(() => {
  today()
})

</script>

<template>
  <Modal ref="modal" :addBtnState="changeNote" />

  <div class="calendar-wrapper">

    <div class="header">
      <div class="header-box">
        <div class="calendar-title">
          <span class="month">{{ monthNames[month] + " " }}</span>
          <span class="year">{{ currentDay.year }}</span>
        </div>

        <div class="nav-bar">
          <div class="today-btn" @click="() => today()">Today</div>

          <div class="arrow-wrapper">
            <div class="past-month" @click="() => previousMonth()"></div>
            <div class="next-month" @click="() => nextMonth()"></div>
          </div>

        </div>
      </div>

      <div class="days-of-week">
        <ul>
          <li>Monday</li>
          <li>Tuesday</li>
          <li>Wensday</li>
          <li>Thusday</li>
          <li>Friday</li>
          <li>Saturday</li>
          <li>Sunday</li>
        </ul>
      </div>
    </div>

    <div class="calendar-body">

      <div class="day" v-for="(item, index) in calendar" :key="index" :class="{ 'today': item.currentDate }">
        <div class="add-btn" @click="activeModal(item, index)">+</div>

        <div class="date">{{ item.day }}</div>

        <div class="note-wrapper">
          <div class="note" v-for="(note, index) in item.notes" :key="index" @click="getNote(index, note)">
            <div class="text-wrapper">
              <p>{{ note.text }}</p>
            </div>
          </div>
        </div>

      </div>
    </div>

  </div>
</template>

<style >
html,
body {
  margin: 0;
  padding: 0;

}

.calendar-wrapper {
  user-select: none;
  font-family: 'Abhaya Libre', serif;
}

.calendar-wrapper .header {
  position: fixed;
  z-index: 4;
  background: #2F3542;
  border: 1px solid #2F3542;
  box-sizing: border-box;
  padding: 25px 20px 10px 20px;
  font-weight: 500;
  top: 0;
  left: 0;
  right: 0;
}

.calendar-wrapper .header .header-box {
  display: flex;
  justify-content: space-between;
  margin: 0 12px 0 30px;
}

.calendar-wrapper .header .header-box .calendar-title .month {
  font-style: normal;
  font-weight: 500;
  font-size: 36px;
  line-height: 42px;
  letter-spacing: 0.05em;
  color: #fff f;
}

.calendar-wrapper .header .header-box .calendar-title .year {
  font-style: normal;
  font-weight: 500;
  font-size: 48px;
  line-height: 57px;
  letter-spacing: 0.05em;
  color: #fff f;
}

.calendar-wrapper .header .header-box .nav-bar {
  display: flex;
}

.calendar-wrapper .header .header-box .nav-bar .arrow-wrapper {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-left: 20px;
}

.calendar-wrapper .header .header-box .nav-bar .arrow-wrapper .past-month {
  padding: 10px 10px 6px 8px;
  margin: 0 23px 5px 0;
  background-color: #A4B0BE;
  border-radius: 50%;
}

.calendar-wrapper .header .header-box .nav-bar .arrow-wrapper .past-month::after {
  content: url('../../src/img/pastMonth.svg');
}

.calendar-wrapper .header .header-box .nav-bar .arrow-wrapper .past-month:hover {
  box-shadow: inset 0px 4px 4px rgba(0, 0, 0, 0.2);
}

.calendar-wrapper .header .header-box .nav-bar .arrow-wrapper .next-month {
  margin: 0 0 5px 25px;
  padding: 10px 8px 6px 10px;
  background-color: #A4B0BE;
  border-radius: 50%;
}

.calendar-wrapper .header .header-box .nav-bar .arrow-wrapper .next-month::after {
  content: url('../../src/img/futureMonth.svg');
}

.calendar-wrapper .header .header-box .nav-bar .arrow-wrapper .next-month:hover {
  box-shadow: inset 0px 4px 4px rgba(0, 0, 0, 0.2);
}

.calendar-wrapper .header .header-box .nav-bar .today-btn {
  padding: 13px 50px 12px;
  background-color: #747D8C;
  border-radius: 3px;
  margin: 6px 52px 6px;
  font-style: normal;
  font-weight: 500;
  font-size: 18px;
  line-height: 21px;
  letter-spacing: 0.05em;
  cursor: pointer;
}

.calendar-wrapper .header .header-box .nav-bar .today-btn:hover {
  box-shadow: inset 0px 4px 4px rgba(0, 0, 0, 0.2);
}

.calendar-wrapper .header .days-of-week {
  margin: 10px 30px 0;
}

.calendar-wrapper .header .days-of-week ul {
  margin-top: 10px;
  display: flex;
  text-align: right;
  justify-content: space-between;
}

.calendar-wrapper .header .days-of-week ul li {
  color: #fff f;
  font-style: normal;
  font-weight: 500;
  font-size: 25px;
  line-height: 29px;
  letter-spacing: 0.09em;
}

.calendar-wrapper .calendar-body {
  padding: 134px 0 0;
  height: 100vh;
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  box-sizing: border-box;
}

.calendar-wrapper .calendar-body .day {
  border-left: 1px solid rgba(47, 53, 66, 0.17);
  border-right: 1px solid rgba(47, 53, 66, 0.17);
  border-bottom: 1px solid rgba(47, 53, 66, 0.17);
  border-top: none;
  cursor: pointer;
  position: relative;
  text-align: center;
  z-index: 1;
}

.calendar-wrapper .calendar-body .day.today {
  background-color: #747D8C;
  box-shadow: inset 0px 20px 35px rgba(33, 106, 56, 0.15);
}

.calendar-wrapper .calendar-body .day.today .date {
  color: #fff f !important;
}

.calendar-wrapper .calendar-body .day .add-btn {
  position: absolute;
  top: 11px;
  right: 5px;
  display: inline-block;
  background-color: #2F3542;
  border-radius: 50%;
  color: white;
  padding: 2px 10px 6px 11px;
  z-index: 5;
  opacity: 0;
  font-family: 'Roboto', sans-serif;
  font-style: normal;
  font-weight: normal;
  font-size: 24px;
  line-height: 28px;
  transition: all 0.3s ease;
}

.calendar-wrapper .calendar-body .day:hover .add-btn {
  opacity: 1;
}

.calendar-wrapper .calendar-body .day .note-wrapper {
  box-sizing: border-box;
  overflow: auto;
  position: absolute;
  height: 70%;
  width: 100%;
  bottom: 0;
  padding: 5px;
  text-align: left;
  z-index: 2;
}

.calendar-wrapper .calendar-body .day .note-wrapper .note {
  display: flex;
  box-sizing: border-box;
  margin: 5px 0;
  width: 100%;
}

.calendar-wrapper .calendar-body .day .note-wrapper .note .text-wrapper {
  box-sizing: border-box;
  overflow-wrap: break-word;
  z-index: 3;
  width: 100%;
  background: #DEDEDE;
  border-radius: 5px;
  box-shadow: inset 0px 4px 4px rgba(0, 0, 0, 0.2);
}

.calendar-wrapper .calendar-body .day .note-wrapper .note .text-wrapper p {
  padding: 5px 10px 5px 5px;
  font-size: 18px;
}

.calendar-wrapper .calendar-body .day .date {
  margin: 15px 0 0 0;
  font-style: normal;
  font-weight: 500;
  font-size: 22px;
  line-height: 26px;
  letter-spacing: 0.05em;
  color: #2F3542;
}

.calendar-wrapper .calendar-body .day:nth-child(7n + 7) .date,
.calendar-wrapper .calendar-body .day:nth-child(7n + 6) .date {
  color: #FF5463;
}

.small-screen-msg {
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  position: fixed;
  display: flex;
  justify-content: center;
  align-items: center;
}

.small-screen-msg .msg p {
  font-size: 25px;
  text-align: center;
}
</style>
