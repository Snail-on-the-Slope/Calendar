<template>
  <div class="datepicker">
    <div class="button-lang" @click.prevent="changeLang">
      <p>{{ curentLang[0] }}</p>
    </div>
    <div class="input-field">
      <input type="text" name="date" placeholder="YYYY-MM-DD" v-model="inputdate" />
      <div class="button button-change-date" @click.prevent="changeDate">
        <p>GO</p>
      </div>
    </div>

    <div class="main-container-wrapper">
      <main>
        <div class="calendar-container">
          <div class="calendar-container__header">
            <button
              class="calendar-container__btn calendar-container__btn--left"
              title="Previous"
              @click.prevent="prevMonth"
            >
              <img src="@/assets/img/back.png" alt="Previous" />
            </button>
            <h2
              class="calendar-container__title"
            >{{curentLang[1].monthName[date.month]}} {{date.year}}</h2>
            <button
              class="calendar-container__btn calendar-container__btn--right"
              title="Next"
              @click.prevent="nextMonth"
            >
              <img src="@/assets/img/next.png" alt="Next" />
            </button>
          </div>
          <div class="calendar-container__body">
            <div class="calendar-table">
              <div class="calendar-table__header">
                <div class="calendar-table__row">
                  <div
                    class="calendar-table__col"
                    v-for="(dayName,i) in curentLang[1].day"
                    :key="i"
                  >{{dayName}}</div>
                </div>
              </div>
              <div class="calendar-table__body">
                <div
                  class="calendar-table__row"
                  v-for="(week, i) in Math.ceil(countDaysMonth/7)"
                  :key="i"
                >
                  <div
                    class="calendar-table__col calendar-table__inactive"
                    v-for="(day, i) in (week === Math.ceil(countDaysMonth/7) ? (7-date.dayEnd) : 0)"
                    :key="i*35"
                  >
                    <div class="calendar-table__item">
                      <span></span>
                    </div>
                  </div>
                  <div
                    class="calendar-table__col"
                    v-for="(day, i) in table_col_main(week)"
                    :key="i+1"
                    :class="{'calendar-table__today': date.day === table__item(week,day), 
                    'calendar-table__event': date.dayActive === table__item(week,day)}"
                  >
                    <div
                      class="calendar-table__item"
                      @click.prevent="newdate(table__item(week,day))"
                    >
                      <span>{{ table__item(week,day) }}</span>
                    </div>
                  </div>

                  <div
                    class="calendar-table__col calendar-table__inactive"
                    v-for="(dayInactive, i) in (week === 1 ? date.dayFirst : 0)"
                    :key="i+100"
                  >
                    <div class="calendar-table__item">
                      <span></span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  name: "datepicker",
  data: () => ({
    lang: {
      ru: {
        monthName: [
          "Январь",
          "Февраль",
          "Март",
          "Апрель",
          "Май",
          "Июнь",
          "Июль",
          "Август",
          "Сентябрь",
          "Октябрь",
          "Ноябрь",
          "Декабрь",
        ],
        day: ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"],
      },
      en: {
        monthName: [
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
          "December",
        ],
        day: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
      },
    },
    curentLang: [],
    date: {
      year: 2020,
      month: 1,
      day: 0,
      dayFirst: 0,
      dayEnd: 0,
      dayActive: 0,
    },
    inputdate: "",
  }),
  created() {
    this.curentLang = ["RU", this.lang.ru]; // язык по умолчанию - русский
    const time = new Date(); // устанавливаем текущую дату
    this.date.year = time.getFullYear();
    this.date.month = time.getMonth();
    this.date.day = time.getDate();
    this.setdateFirstAndEnd();
  },
  computed: {
    // проверка года на високосность
    leap_year() {
      return new Date(this.date.year, 1, 29).getMonth() === 1 ? 29 : 28;
    },
    // колличество дней в месяце
    countDaysMonth() {
      return [31, this.leap_year, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31][
        this.date.month
      ];
    },
  },
  methods: {
    // изменение языка
    changeLang() {
      this.curentLang[0] === "RU"
        ? (this.curentLang = ["EN", this.lang.en])
        : (this.curentLang = ["RU", this.lang.ru]);
    },
    // Нахождение первого и последнего дней месяца
    setdateFirstAndEnd() {
      let dayFirst = new Date(this.date.year, this.date.month).getDay();
      this.date.dayFirst = dayFirst === 0 ? 6 : dayFirst - 1;

      let dayEnd = new Date(
        this.date.year,
        this.date.month,
        this.countDaysMonth + 1
      ).getDay();
      this.date.dayEnd = dayEnd === 0 ? 6 : dayEnd - 1;
      this.date.dayActive = 0;
    },
    // Изменение Месяца (если новый = текущему - добавление 'calendar-table__today')
    changeMonth() {
      this.date.year === new Date().getFullYear() &&
      this.date.month === new Date().getMonth()
        ? (this.date.day = new Date().getDate())
        : (this.date.day = 0);
      this.setdateFirstAndEnd();
    },
    // Изменение месяца ('calendar-container__btn')
    prevMonth() {
      if (this.date.month === 0) {
        this.date.month = 11;
        this.date.year -= 1;
      } else {
        this.date.month -= 1;
      }
      this.changeMonth();
    },
    // Изменение месяца ('calendar-container__btn')
    nextMonth() {
      if (this.date.month === 11) {
        this.date.month = 0;
        this.date.year += 1;
      } else {
        this.date.month += 1;
      }
      this.changeMonth();
    },
    // Валидация данных по шаблону yyyy-mm-dd
    isValidDate(dateString) {
      let regEx = /^\d{4}-\d{2}-\d{2}$/;
      if (!dateString.match(regEx)) return false;
      let d = new Date(dateString);
      let dNum = d.getTime();
      if (!dNum && dNum !== 0) return false;
      return d.toISOString().slice(0, 10) === dateString;
    },
    // Переход к новой дате ('input-field' > input[type="text"])
    changeDate() {
      this.isValidDate(this.inputdate)
        ? ((this.date.year = +this.inputdate.substring(0, 4)),
          (this.date.month = this.inputdate.substring(5, 7) - 1),
          this.changeMonth(),
          (this.date.dayActive = +this.inputdate.substring(8, 10)))
        : this.curentLang[0] === "RU"
        ? (this.inputdate = this.inputdate + "  Неверный формат (2020-08-12)")
        : (this.inputdate = this.inputdate + "  Wrong format (2020-08-12)");
    },
    // Изменение текста в ('input-field' > input[type="text"]) при клике по календарю
    newdate(e) {
      this.inputdate =
        this.date.year +
        "-" +
        (this.date.month < 9
          ? "0" + (this.date.month + 1)
          : this.date.month + 1) +
        "-" +
        (e < 10 ? "0" + e : e);
      this.date.dayActive = e;
    },
    // Вычесление значения для v-for ('calendar-table__col')
    table_col_main(week) {
      return week === 1
        ? 7 - this.date.dayFirst
        : week === Math.ceil(this.countDaysMonth / 7)
        ? this.date.dayEnd
        : 7;
    },
    // Вычесление значения каждого дня месяца
    table__item(week, day) {
      return week === 1
        ? 7 - this.date.dayFirst - day + 1
        : -this.date.dayFirst - day + 7 * week + 1;
    },
  },
};
</script>