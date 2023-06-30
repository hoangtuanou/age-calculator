<template>
  <div class="card-wrapper">
    <div class="header">
      <div class="input-wrapper">
        <label for="day">day</label>
        <input type="number" name="day" @input="update($event, 'day')">
      </div>
      <div class="input-wrapper">
        <label for="month">month</label>
        <input type="number" name="month" @input="update($event, 'month')">
      </div>
      <div class="input-wrapper">
        <label for="year">year</label>
        <input type="number" name="year" @input="update($event, 'year')">
      </div>
    </div>
    <div class="divide">
      <IconArrowDown />
    </div>

    <div class="line" v-if="yearDiff">
      <span class="number">{{ yearDiff }}</span>
      <span class="text">years</span>
    </div>
    <div class="line" v-if="monthDiff">
      <span class="number">{{ monthDiff }}</span>
      <span class="text">months</span>
    </div>
    <div class="line" v-if="dayDiff">
      <span class="number">{{ dayDiff }}</span>
      <span class="text">days</span>
    </div>
  </div>
</template>

<style scoped lang="scss">
.card-wrapper {
  width: 90%;
  margin: 0 auto;
  border-radius: 15px;
  border-bottom-right-radius: 50px;
  background-color: var(--white);
  padding: 30px 20px;
}

.header {
  display: flex;
  gap: 15px;
}

.input-wrapper {
  label {
    text-transform: uppercase;
    color: var(--off-black);
  }

  input {
    width: 100%;
    padding: 5px 15px;
    border: 1px solid var(--light-grey);
    border-radius: 5px;
    font-size: 2rem;
    margin-top: 5px;
  }
}

.divide {
  width: 100%;
  height: 2px;
  background-color: var(--light-grey);
  position: relative;
  margin: 50px 0;

  .circle {
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
  }
}

.line {
  font-size: 40px;
  font-weight: 800;
  font-style: italic;

  .number {
    color: var(--purple);
  }

  .text {
    color: var(--black);
  }
}
</style>

<script setup lang="ts">
import IconArrowDown from './icons/IconArrow.vue'
</script>

<script lang="ts">
const getDayDiff = (startDate: Date, endDate: Date) => {
  return Math.abs(endDate.getDate() - startDate.getDate());
}

const getYearDiff = (startDate: Date, endDate: Date) => {
  return Math.round(
    Math.abs(endDate.getFullYear() - startDate.getFullYear())
  );
}

const getMonthDiff = (startDate: Date, endDate: Date) => {
  return Math.abs(endDate.getMonth() - startDate.getMonth())
}

const validateDate = ({ day, month, year }) => {
  let ListofDays = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
  if (month === 1 || month > 2) {
    if (day > ListofDays[month - 1]) {
      return false;
    }
  } else if (month == 2) {
    let leapYear = false;
    if ((!(year % 4) && year % 100) || !(year % 400)) leapYear = true;
    if ((leapYear == false) && (day >= 29)) return false;
    else
      if ((leapYear == true) && (day > 29)) {
        console.log('Invalid date format!');
        return false;
      }
  }

  return true
}

export default {
  data() {
    return {
      date: {
        day: 0,
        month: 0,
        year: 0,
      },
      dayDiff: 0,
      monthDiff: 0,
      yearDiff: 0,
    }
  },
  watch: {
    date: {
      handler(newDate) {
        const inputHaveValue = Object.values(newDate).filter(value => !!value)
        if (inputHaveValue.length === 3) {
          const isValid = validateDate(newDate)

          if (isValid) {
            this.calculateAge()
          } else {
            console.log('INVALID')
          }
        }
      },
      deep: true,
    },
  },
  methods: {
    update(event: Event, key: 'day' | 'month' | 'year') {
      this.date[key] = +(event.target as HTMLInputElement).value
    },
    calculateAge() {
      const { day, month, year } = this.date
      const startDate = new Date(`${year}-${month}-${day}`)
      const now = new Date()

      this.dayDiff = getDayDiff(startDate, now)
      this.yearDiff = getYearDiff(startDate, now)
      this.monthDiff = getMonthDiff(startDate, now)
    }
  }
}
</script>