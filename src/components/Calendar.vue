<template>
  <div>
    <Calendar
      lunar
      monFirst
      responsive
      :monthRange="monthRange"
      :tileContent="tileContent"
      :events="events"
      :almanacs="almanacs"
      ref="calendar"
      :rangeMonthFormat="rangeMonthFormat"
      :value="value"
    />
    <!-- <button @click="setToday">返回今日</button>
    <button @click="dateInfo">日期信息</button>
    <button @click="renderer">重新渲染年月日期</button>-->
  </div>
</template>

<script>
import Calendar from "mpvue-calendar";
import "mpvue-calendar/src/browser-style.css";
// import arrowLeft from '../assets/arrowLeft.png' //使用自定义箭头图片

export default {
  mounted() {
    let { weekList, events } = this.initData(
      "2020-9-7",
      "2021-1-11",
      14,
      "2020-11-9",
      14
    );
    this.tileContent = weekList;
    this.events = events;
    // this.tileContent = zc
  },
  data() {
    return {
      value: [
        [2020, 6, 21],
        [2020, 6, 28]
      ],
      rangeMonthFormat: "yyyy年MM月",
      almanacs: {},
      events: {},
      monthRange: ["2020-9", "2021-7"],
      tileContent: []
    };
  },
  components: {
    Calendar
  },
  methods: {
    // firstWeek        当前学期的第一天
    // examweek         考试周的第一天
    // examWeekLastTime 考试周持续时间
    //lbWeek            礼拜周第一天
    //lbWeekLastTime    礼拜周持续时间
    initData(firstWeek, examWeek, examWeekLastTime, lbWeek, lbWeekLastTime) {
      let weekDate = firstWeek;
      let weekList = [];
      let events = {};
      function getNewDay(curWeek, days) {
        var dateTemp = curWeek.split("-");
        var nDate = new Date(
          dateTemp[1] + "-" + dateTemp[2] + "-" + dateTemp[0]
        ); //转换为MM-DD-YYYY格式
        var millSeconds = Math.abs(nDate) + days * 24 * 60 * 60 * 1000;
        var rDate = new Date(millSeconds);
        var year = rDate.getFullYear();
        var month = rDate.getMonth() + 1;
        // if (month < 10) month = "0" + month;
        var date = rDate.getDate();
        // if (date < 10) date = "0" + date;
        return year + "-" + month + "-" + date;
      }
      //周次
      for (let i = 0; i < 20; i++) {
        weekList.push({
          date: getNewDay(weekDate, 7 * i),
          className: "holiday"
          // content: "休"
        });
        events[getNewDay(weekDate, 7 * i)] = `第${i + 1}周`;
      }
      //考试周
      for (let k = 0; k < examWeekLastTime; k++) {
        //考试周
        weekList.push({
          date: getNewDay(examWeek, k),
          className: "exam",
          content: "考"
        });
      }
      //礼拜周
      for (let j = 0; j < lbWeekLastTime; j++) {
        //考试周
        weekList.push({
          date: getNewDay(lbWeek, j),
          className: "lbWeek",
          content: "礼"
        });
      }
      //周次变红
      weekList.map(res => {
        if (examWeekLastTime > 8 || lbWeekLastTime > 8) {
          //有两个考试周
          if (res.date == examWeek || res.date == getNewDay(examWeek, 7)) {
            res.content = "考";
            res.className = "exam";
          }
          //有两个礼拜周
          if (res.date == lbWeek || res.date == getNewDay(lbWeek, 7)) {
            res.content = "礼";
            res.className = "lbWeek";
          }
        } else {
          if (res.date == examWeek) {
            //只有一个考试周
            res.content = "考";
            res.className = "exam";
          }
          if (res.date == lbWeek) {
            //只有一个礼拜周
            res.content = "礼";
            res.className = "lbWeek";
          }
        }
      });
      return {
        weekList,
        events
      };
    }
  }
};
</script>
<style >
* {
  padding: 0;
  margin: 0;
}
.lbWeek {
  background-color: #e6f7ff;
}
.lbWeek span {
  /* color: #91d5ff; */
}
.lbWeek .slot-element{
  background-color: #0050b3;
}
.exam {
  background-color: #fff0f0;
}
.exam span {
  color: red;
}
.holiday {
  background-color: #adc6ff;
}
.slot-element {
  position: absolute;
  top: 0;
  left: 0;
  font-size: 12px;
  background-color: #f43;
  color: #fff;
  padding: 0 2px;
  border-radius: 3px;
  display: inline-block;
}
</style>