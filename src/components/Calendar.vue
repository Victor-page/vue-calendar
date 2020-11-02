<template>
  <div class="calendar">
    <div class="actions">
      <button class="actions-btn" @click="moveThisMonth">
        Today
      </button>
      <button class="actions-btn" @click="movePreviousYear">
        Previous Year
      </button>
      <button class="actions-btn" @click="movePreviousMonth">
        Previous Month
      </button>
      <button class="actions-btn" @click="moveNextMonth">
        Next Month
      </button>
      <button class="actions-btn" @click="moveNextYear">
        Next Year
      </button>
    </div>
    <div class="header">
      <a class="arrow" @click="movePreviousYear">&laquo;</a>
      <a class="arrow" @click="movePreviousMonth">&lsaquo;</a>
      <span class="title" @click="moveThisMonth">
        {{ header.label }}
      </span>
      <a class="arrow" @click="moveNextMonth">&rsaquo;</a>
      <a class="arrow" @click="moveNextYear">&raquo;</a>
    </div>
    <div class="weekdays">
      <div class="weekday" v-for="weekday in weekdays">
        {{ weekday.label_3 }}
      </div>
    </div>
    <div class="week" v-for="week in weeks">
      <div
        class="day"
        :class="{ today: day.isToday, 'not-in-month': !day.inMonth }"
        v-for="day in week"
        @click="handleDayClick(day)"
      >
        <span>{{ day.day }}</span>
        <ol>
          <li v-for="note in day.notes">
            <p>{{ note }}</p>
          </li>
        </ol>
      </div>
    </div>
  </div>
</template>

<script>
// Calendar data
const _daysInMonths = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
const _weekdayLabels = [
  "Sunday",
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday"
];
const _monthLabels = [
  "January",
  "February",
  "March",
  "April",
  "May",
  "June",
  "July",
  "August",
  "September",
  "October",
  "November",
  "December"
];
const _today = new Date();
const _todayComps = {
  year: _today.getFullYear(),
  month: _today.getMonth() + 1,
  day: _today.getDate()
};

export default {
  data: function() {
    return {
      month: _todayComps.month,
      year: _todayComps.year
    };
  },
  computed: {
    monthIndex() {
      return this.month - 1;
    },
    isLeapYear() {
      return (
        (this.year % 4 === 0 && this.year % 100 !== 0) || this.year % 400 === 0
      );
    },
    previousMonthComps() {
      if (this.month === 1)
        return {
          days: _daysInMonths[11],
          month: 12,
          year: this.year - 1
        };
      return {
        days:
          this.month === 3 && this.isLeapYear
            ? 29
            : _daysInMonths[this.month - 2],
        month: this.month - 1,
        year: this.year
      };
    },
    nextMonthComps() {
      if (this.month === 12)
        return {
          days: _daysInMonths[0],
          month: 1,
          year: this.year + 1
        };
      return {
        days:
          this.month === 2 && this.isLeapYear ? 29 : _daysInMonths[this.month],
        month: this.month + 1,
        year: this.year
      };
    },
    months() {
      return _monthLabels.map((ml, i) => ({
        label: ml,
        label_1: ml.substring(0, 1),
        label_2: ml.substring(0, 2),
        label_3: ml.substring(0, 3),
        number: i + 1
      }));
    },
    weekdays() {
      return _weekdayLabels.map((wl, i) => ({
        label: wl,
        label_1: wl.substring(0, 1),
        label_2: wl.substring(0, 2),
        label_3: wl.substring(0, 3),
        number: i + 1
      }));
    },
    header() {
      const month = this.months[this.monthIndex];
      return {
        month: month,
        year: this.year.toString(),
        shortYear: this.year.toString().substring(2, 4),
        label: month.label + " " + this.year
      };
    },
    firstWeekdayInMonth() {
      return new Date(this.year, this.monthIndex, 1).getDay() + 1;
    },
    daysInMonth() {
      if (this.month === 2 && this.isLeapYear) return 29;
      return _daysInMonths[this.monthIndex];
    },
    weeks() {
      const weeks = [];
      let previousMonth = true,
        thisMonth = false,
        nextMonth = false;
      let day = this.previousMonthComps.days - this.firstWeekdayInMonth + 2;
      let month = this.previousMonthComps.month;
      let year = this.previousMonthComps.year;
      for (let w = 1; w <= 6 && !nextMonth; w++) {
        const week = [];
        for (let d = 1; d <= 7; d++) {
          if (previousMonth && d >= this.firstWeekdayInMonth) {
            day = 1;
            month = this.month;
            year = this.year;
            previousMonth = false;
            thisMonth = true;
          }
          week.push({
            notes: [],
            label: day && thisMonth ? day.toString() : "",
            day,
            weekday: d,
            week: w,
            month,
            year,
            date: new Date(year, month - 1, day),
            beforeMonth: previousMonth,
            afterMonth: nextMonth,
            inMonth: thisMonth,
            isToday:
              day === _todayComps.day &&
              month === _todayComps.month &&
              year === _todayComps.year,
            isFirstDay: thisMonth && day === 1,
            isLastDay: thisMonth && day === this.daysInMonth
          });
          if (thisMonth && day >= this.daysInMonth) {
            thisMonth = false;
            nextMonth = true;
            day = 1;
            month = this.nextMonthComps.month;
            year = this.nextMonthComps.year;
          } else {
            day++;
          }
        }
        weeks.push(week);
      }
      return weeks;
    }
  },
  methods: {
    moveThisMonth() {
      this.month = _todayComps.month;
      this.year = _todayComps.year;
    },
    moveNextMonth() {
      const { month, year } = this.nextMonthComps;
      this.month = month;
      this.year = year;
    },
    movePreviousMonth() {
      const { month, year } = this.previousMonthComps;
      this.month = month;
      this.year = year;
    },
    moveNextYear() {
      this.year++;
    },
    movePreviousYear() {
      this.year--;
    },
    handleDayClick(day) {
      const chosenDateText = `${new Date(day.date).getDate()}/${this.month}/${
        this.year
      }`;
      this.$emit("clickedDate", chosenDateText);
      this.$emit("chosenDay", day);
      this.$forceUpdate();
    }
  }
};
</script>

<style lang="scss" scoped>
.calendar {
  display: flex;
  flex-direction: column;
  padding: 10px;
}

.header {
  display: flex;
  justify-content: stretch;
  align-items: center;
  padding: 0.5rem 1rem;
  border: 1px solid #eaf0f4;
}

.arrow {
  padding: 0 0.4em 0.2em 0.4em;
  font-size: 1.8rem;
  font-weight: 500;
  user-select: none;
  flex-grow: 0;
  cursor: pointer;
}

.arrow:hover,
.arrow:active {
  color: #3b86ff;
}

.title {
  flex-grow: 1;
  font-size: 1.2rem;
  text-align: center;
  cursor: pointer;
}

.title:hover {
  color: #3b86ff;
}

.weekdays {
  display: flex;
}

.weekday {
  width: 14.2857%;
  display: flex;
  justify-content: center;
  padding: 15px 0;
  color: #a3a6b4;
  border-bottom: 1px solid #eaf0f4;
  background-color: #f5f6fa;
  font-size: 0.75rem;
  font-weight: 700;
  text-transform: uppercase;
  cursor: default;
}

.week {
  display: flex;
}

.day {
  width: 14.2857%;
  min-height: 140px;
  display: flex;
  flex-direction: column;
  padding: 10px;
  color: #3a3a3a;
  background-color: white;
  border: 1px solid #eaf0f4;
  cursor: default;
}

.day span {
  display: block;
  align-self: flex-end;
  color: #3a3a3a;
}

.day li {
  margin-bottom: 4px;
}

.day p {
  margin: 0;
  word-wrap: break-word;
}

.today {
  font-weight: 500;
  color: white;
  background-color: #a3a6b4;
}

.not-in-month {
  color: #cacaca;
  background-color: #fafafa;
}

.actions {
  display: flex;
  margin-bottom: 5px;
}

.actions-btn {
  background: #fff;
  padding: 8px 18px;
  border: 1px solid #d7dae2;
  box-shadow: 0 2px 3px rgba(0, 0, 0, 0.05);
  font-size: 13px;
  transition: all 0.3s ease-in-out;
  cursor: pointer;
}

.actions-btn:not(:last-child) {
  border-right: none;
}

.actions-btn:first-child {
  border-bottom-left-radius: 4px;
  border-top-left-radius: 4px;
}

.actions-btn:last-child {
  border-bottom-right-radius: 4px;
  border-top-right-radius: 4px;
}

.actions-btn:hover,
.actions-btn:active {
  color: #3b86ff;
}
</style>
