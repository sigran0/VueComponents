<template>
    <div class="base-calendar-wrapper base-calendar-text">
        <!-- 타이틀 영역 -->
        <h2 class="base-calendar-text">
            <span class="base-calendar-text-indicator" @click="modifyMonth(-1)">◀</span>
            {{ currentYear }} 년 {{ currentMonth + 1 }} 월
            <span class="base-calendar-text-indicator" @click="modifyMonth(1)">▶</span>
        </h2>

        <!-- 달력 영역-->
        <div class="base-calendar-content-wrapper">
            <table>
                <!-- 달력 머리 부분 -->
                <thead>
                <tr>
                    <td
                            class="base-calendar-content-header-wrapper"
                            v-for="(item, index) in calendarHeaderTextList"
                            :key="`${index}-calendar-header-text`"
                            :class="{ sunday: isSunday(index), saturday: isSaturday(index) }">
                        {{ item }}
                    </td>
                </tr>
                </thead>
                <!-- 달력 몸통 부분 -->
                <tbody class="bases-calendar-content-body-wrapper">
                <tr
                        v-for="(dates, weekIndex) in weekList"
                        :key="`${ weekIndex }-calendar-week`">
                    <td
                            v-for="(date, dateIndex) in dates"
                            :key="`${ dateIndex }-calendar-date`"
                            :class="{ sunday: isSunday(dateIndex), saturday: isSaturday(dateIndex) }">
                        <transition name="fade" appear mode="in-out">
                            <button
                                    :class="{ marked: isMarked(date), today: isToday(date) }"
                                    @dblclick="onDoubleClickDate(date)"
                                    @click="onClickDate(date)">
                                {{ date > 0 ? date : '' }}
                            </button>
                        </transition>
                    </td>
                </tr>
                </tbody>

            </table>

        </div>

    </div>
</template>

<script>
    export default {
        name: 'BaseCalendar',
        props: {
            OnClickDate: {
                type: Function,
                default: null
            },
            OnDoubleClickDate: {
                type: Function,
                default: null
            },
            MarkedDateList: {
                type: Array,
                default: () => []
            }
        },
        data () {
            return {
                currentYear: 2019,
                currentMonth: 9,
                calendarHeaderTextList: ['S', 'M', 'T', 'W', 'T', 'F', 'S'],
                targetDate: new Date()
            }
        },
        watch: {
            currentMonth (value) {
                if (value > 11) {
                    this.currentMonth = 0
                    this.currentYear += 1
                } else if (value < 0) {
                    this.currentMonth = 11
                    this.currentYear -= 1
                }
            },
            selectedDate (value) {
                this.currentYear = value.getFullYear()
                this.currentMonth = value.getMonth()
            }
        },
        computed: {
            selectedDate () {
                return this.$attrs.value || this.targetDate
            },
            targetStartDate () {
                return new Date(this.currentYear, this.currentMonth, 1)
            },
            targetLastDate () {
                return new Date(this.currentYear, this.currentMonth + 1, 0)
            },
            startDayInThisMonth () {
                return this.targetStartDate.getDay()
            },
            dayLengthInThisMonth () {
                return this.targetLastDate.getDate() - this.targetStartDate.getDate() + 1
            },
            markedDateStringList () {
                return this.MarkedDateList.map(item => {
                    if (item instanceof Date) {
                        return item.toDateString()
                    }
                })
            },
            weekList () {
                // eslint-disable-next-line no-return-assign
                const dateList = [...Array(this.dayLengthInThisMonth).keys()].map(item => item += 1)
                const result = [[]]
                let dayIndicator = this.startDayInThisMonth
                let rowIndex = 0

                // 앞부분 빈칸처리
                result[0] = [].concat(new Array(dayIndicator).fill(-1))

                // 각 Row 별로 날짜 입력
                dateList.forEach(date => {
                    if (dayIndicator > 6) {
                        dayIndicator = 0
                        rowIndex += 1
                        result.push([])
                    }
                    result[rowIndex].push(date)
                    dayIndicator += 1
                })

                return result
            }
        },
        methods: {
            isSunday (index) {
                return index === 0
            },
            isSaturday (index) {
                return index === 6
            },
            isToday (date) {
                return this.selectedDate.toDateString() === new Date(this.currentYear, this.currentMonth, date).toDateString()
            },
            isMarked (date) {
                return this.markedDateStringList.includes(new Date(this.currentYear, this.currentMonth, date).toDateString())
            },
            modifyMonth (amount) {
                this.currentMonth += amount
            },
            onClickDate (date) {
                const resultDate = new Date(this.currentYear, this.currentMonth, date)

                if (this.$attrs.value !== undefined) {
                    this.$emit('input', resultDate)
                } else {
                    this.targetDate = resultDate
                }

                if (this.OnClickDate !== null) {
                    this.OnClickDate(resultDate)
                }
            },
            onDoubleClickDate (date) {
                const resultDate = new Date(this.currentYear, this.currentMonth, date)

                if (this.OnDoubleClickDate !== null) {
                    this.OnDoubleClickDate(resultDate)
                }
            }
        },
        mounted () {
            this.currentYear = this.selectedDate.getFullYear()
            this.currentMonth = this.selectedDate.getMonth()
        }
    }
</script>

<style scoped>
    .base-calendar-wrapper {
        width: 100%;
        padding-top: 86px;
        padding-bottom: 86px;

        text-align: center;
    }

    .base-calendar-text {
        color: #282828;
        font-size: 42px;
        vertical-align: center;
    }

    .base-calendar-text-indicator {
        font-size: 50px;
        margin-left: 90px;
        margin-right: 90px;
    }

    .base-calendar-content-wrapper {
        margin-top: 71px;
        padding-left: 55px;
        padding-right: 55px;
    }

    .base-calendar-content-header-wrapper {
        padding-left: 55px;
        padding-right: 55px;
    }

    .bases-calendar-content-body-wrapper {
    }

    .bases-calendar-content-body-wrapper > tr {
        height: 100px;
    }

    .bases-calendar-content-body-wrapper > tr > td {
    }

    .sunday {
        color: red;
        transition: 0.5s;
    }

    .saturday {
        color: #1c71ff;
        transition: 0.5s;
    }

    .marked {
        background-color: #dcdcdc;
        border-radius: 50%;
        width: 85px;
        height: 85px;
    }

    .today {
        background-color: #188cff;
        border-radius: 50%;
        width: 85px;
        height: 85px;
        transition: 0.5s;
    }

    .fade-enter-active,
    .fade-leave-active {
        transition: all .5s;
    }
    .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
        transform: scale(0.0);
        opacity: 0;
    }

</style>
