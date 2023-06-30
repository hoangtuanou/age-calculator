<template>
  <div class="card-wrapper">
    <div class="header">
      <div class="input-wrapper" :class="isDayValid ? '' : 'error'">
        <label for="day">day</label>
        <input type="number" name="day" @input="update($event, 'day')" placeholder="DD">
        <span class="error-message" v-if="!isDayValid">Must be a valid day</span>
      </div>
      <div class="input-wrapper" :class="isMonthValid ? '' : 'error'">
        <label for="month">month</label>
        <input type="number" name="month" @input="update($event, 'month')" placeholder="MM">
        <span class="error-message" v-if="!isMonthValid">Must be a valid month</span>
      </div>
      <div class="input-wrapper" :class="isYearValid ? '' : 'error'">
        <label for="year">year</label>
        <input type="number" name="year" @input="update($event, 'year')" placeholder="YYYY">
        <span class="error-message" v-if="!isYearValid">Must be in the part</span>
      </div>
    </div>
    <div class="divide">
      <IconArrowDown />
    </div>

    <div class="line">
      <span class="number">{{ yearDiff && isYearValid && year ? yearDiff : '--' }}</span>
      <span class="text">years</span>
    </div>
    <div class="line">
      <span class="number">{{ monthDiff && isMonthValid && month ? monthDiff : '--' }}</span>
      <span class="text">months</span>
    </div>
    <div class="line">
      <span class="number">{{ dayDiff && isDayValid && day ? dayDiff : '--' }}</span>
      <span class="text">days</span>
    </div>
  </div>
</template>

<style scoped lang="scss">
@import '../styles/responsive.scss';

.card-wrapper {
  width: min(90%, 550px);
  margin: 0 auto;
  border-radius: 15px;
  border-bottom-right-radius: 30%;
  background-color: var(--white);
  padding: 30px 20px;
  transform: translateY(10vh);

  @include desktop {
    padding: 32px;
  }
}

.header {
  display: flex;
  gap: 15px;
}

.input-wrapper {
  display: flex;
  flex-direction: column;
  font-weight: 500;

  label {
    text-transform: uppercase;
    color: var(--off-black);
  }

  input {
    width: 100%;
    padding: 5px 15px;
    border: 1px solid var(--light-grey);
    border-radius: 5px;
    font-size: 1.5rem;
    margin-top: 5px;
    caret-color: var(--purple);
    transition: all 0.2s ease-in;

    &:focus {
      border-color: var(--purple);
    }

    @include desktop {
      width: 120px;
    }
  }

  .error-message {
    color: var(--light-red);
    font-weight: 300;
    font-size: 0.7rem;
    margin-top: 5px;
  }
}

.error {
  label {
    color: var(--light-red);
  }

  input {
    border-color: var(--light-red);

    &:focus {
      border-color: var(--light-red);
    }
  }
}

.divide {
  width: 100%;
  height: 2px;
  background-color: var(--light-grey);
  position: relative;
  margin: 50px 0;

  @include desktop {
    margin: 35px 0;
  }

  .circle {
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);

    @include desktop {
      left: auto;
      right: 0;
      transform: translate(0, -50%);
    }
  }
}

.line {
  font-size: 40px;
  font-weight: 800;
  font-style: italic;
  display: flex;
  gap: 10px;

  .number {
    color: var(--purple);
  }

  .text {
    color: var(--black);
  }

  @include desktop {
    font-size: 60px;
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

const validateDay = ({ day, month, year }: { day: number, month: number, year: number }) => {
  let ListofDays = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];

  if (day > 31) return false

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
      day: 0,
      month: 0,
      year: 0,
      dayDiff: 0,
      monthDiff: 0,
      yearDiff: 0,
      isDayValid: true,
      isMonthValid: true,
      isYearValid: true,
    }
  },
  watch: {
    day: {
      handler(newDay) {
        const isValid = validateDay({ day: newDay, month: this.month || 0, year: this.year || 0 })

        if (isValid) {
          if (this.month && this.year) {
            this.calculateAge()
            this.isDayValid = true
          }
        } else {
          this.isDayValid = false
        }
      }
    },
    month: {
      handler(newMonth) {
        if (newMonth <= 12) {
          if (this.day && this.year) {
            this.calculateAge()
            this.isMonthValid = true
          }
        } else {
          this.isMonthValid = false
        }
      }
    },
    year: {
      handler(newYear) {
        const currentDate = new Date()

        if (newYear <= currentDate.getFullYear()) {
          if (this.day && this.month) {
            this.calculateAge()
            this.isYearValid = true
          }
        } else {
          this.isYearValid = false
        }
      }
    }
  },
  methods: {
    update(event: Event, key: 'day' | 'month' | 'year') {
      this[key] = +(event.target as HTMLInputElement).value
    },
    calculateAge() {
      const { day, month, year } = this
      const startDate = new Date(`${year}-${month}-${day}`)
      const now = new Date()

      this.dayDiff = getDayDiff(startDate, now)
      this.yearDiff = getYearDiff(startDate, now)
      this.monthDiff = getMonthDiff(startDate, now)
    }
  }
}
</script>