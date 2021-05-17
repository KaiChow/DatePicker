<template>
  <div v-click-outside>
    <input type="text" :value="formateDate" />
    <div v-if="isVisible" class="panel">
      <div class="panel-nav">
        <span>&lt;</span>
        <span>&lt;&lt;</span>
        <span>{{time.year}}年</span>
        <span>{{time.month+1}}月</span>
        <span>&gt;&gt;</span>
        <span>&gt;</span>
      </div>
      <div class="panel-content">
        <div class="days">
          <div class="week-item">
            <span v-for="k in 7" :key="k">
              {{ weekDays[k - 1] }}
            </span>
          </div>
          <div v-for="i in 6" :key="i" class="cell">
            <span
            @click="chooseDay(currentDays[(i - 1) * 7 + (j - 1)])"
              v-for="j in 7"
              :key="j"
              :class="[
                { current: isCurrentDay(currentDays[(i - 1) * 7 + (j - 1)]) },
                {
                  isnotcurrent: !isNotCurrentMonth(
                    currentDays[(i - 1) * 7 + (j - 1)]
                  ),
                },
              ]"
              >{{ currentDays[(i - 1) * 7 + (j - 1)].getDate() }}</span
            >
          </div>
        </div>
      </div>
      <div class="panel-footer" @click="chooseToday">今天</div>
    </div>
  </div>
</template>

<script>
import * as utils from './utils';
export default {
  props: {
    value: {
      type: Date,
      default: () => new Date(),
    },
  },
  data() {
    let {year,month}  = utils.getYearMonthDay(this.value)
    return {
      isVisible: false,
      time:{year,month},
      weekDays: ['日', '一', '二', '三', '四', '五', '六'],
    };
  },
  directives: {
    clickOutside: {
      bind(el, bindings, vnode) {
        let handler = (e) => {
          //  console.log(e.target);
          if (el.contains(e.target)) {
            if (!vnode.context.isVisible) {
              vnode.context.focus();
            }
          } else {
            if (vnode.context.isVisible) {
              vnode.context.blur();
            }
          }
        };
        el.handler = handler;
        document.addEventListener('click', handler);
      },
      unbind(el) {
        document.removeEventListener('click', el.handler);
      },
    },
  },
  computed: {
    formateDate() {
      const { year, month, day } = utils.getYearMonthDay(this.value);
      return `${year}-${month + 1}-${day}`;
    },

    currentDays() {
      const { year, month } = utils.getYearMonthDay(new Date());

      //  当前的第一天
      const currenMonthFirstDay = new Date(year, month, 1);

      const week = currenMonthFirstDay.getDay();

      const startDay = currenMonthFirstDay - (week - 1) * 60 * 60 * 1000 * 24;

      let arr = [];

      for (let i = 0; i < 42; i++) {
        arr.push(new Date(startDay + 60 * 60 * 1000 * 24 * i));
      }

      return arr;
      //  6*7  42天
    },
  },

  methods: {
    focus() {
      this.isVisible = true;
    },
    blur() {
      this.isVisible = false;
    },
    isCurrentDay(date) {
      const { year, month, day } = utils.getYearMonthDay(this.value);
      const { year: y, month: m, day: d } = utils.getYearMonthDay(date);
      return year === y && month === m && day === d;
    },
    isNotCurrentMonth(date) {
      const { year, month } = utils.getYearMonthDay(this.value);
      const { year: y, month: m } = utils.getYearMonthDay(date);
      return year === y && month === m;
    },
    chooseDay(date){
      this.time = utils.getYearMonthDay(date)
      this.$emit('input',date)
      this.blur()
    },
    chooseToday(){
       this.$emit('input',new Date())
      this.blur()
    }
  },
};
</script>

<style lang="scss">
.panel {
  background-color: #fff;
  display: flex;
  flex-direction: column;
  &-content {
    .days {
      .week-item {
        span {
          width: 32px;
          height: 32px;
          display: inline-flex;
          justify-content: center;
          align-items: center;
        }
      }
      .cell {
        span {
          width: 32px;
          height: 32px;
          display: inline-flex;
          justify-content: center;
          align-items: center;
          cursor: pointer;
          box-sizing: border-box;
          border: 1px solid #fff;
          &:hover {
            border-radius: 4px;
            border: 1px solid rgb(236, 200, 200);
          }
        }
        .current {
          border-radius: none;
          background-color: blue;
          color: #fff;
        }
        .isnotcurrent {
          color: gray;
          opacity: 0.7;
        }
      }
    }
  }
}
</style>
