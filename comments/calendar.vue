<template>
    <div class="calendar-container">
        <div class="year">
            <div>
                <span class="fl" @click="lastYear">«</span>
                <span class="fl" @click="lastMonth">‹</span>
                <p>{{nowDate.year}}年{{nowDate.month+1}}月</p>
                <span class="fr" @click="nextYear">»</span>
                <span class="fr" @click="nextMonth">›</span>
            </div>
        </div>
        <ul class="week">
           <li v-for="(o,index) in 7" :key="o">{{formatWeek(index)}}</li> 
        </ul>
        <ul class="date">
            <li class="none-week" v-for="o in lastMonthDays" :key="o+50" >{{lastMonthStartDay+o-1}}</li>
            <li @click="clickEvent" v-for="day in nowMonthDays" :key="day" >{{day}}</li>
            <li class="none-week" v-for="day in (42-lastMonthDays-nowMonthDays)" :key="day+100">{{day}}</li>
        </ul>
    </div>
</template>

<script>
export default {
    props: ['start-date'],
    data () {
        return {
            selectDate: [],//选择日期列表
            nowDate: this.getDate(new Date()), //当前设置时间 默认为当前系统时间
        }
    },
    computed: {
        lastMonthDays () {
            return this.startWeek()
        },
        lastMonthStartDay () {
            return this.calcLastMonthDays(this.nowDate.year,this.nowDate.month)-(this.startWeek()-1)
        },
        nowMonthDays () {
            return this.calcDays(this.nowDate.year,this.nowDate.month)
        }
    },
    created () {
        if(this.setDate) {
            this.nowDate = this.getDate(this.setDate)
        }
    },
    methods: {
        getDate (date) {
            return {
                year: date.getFullYear(),
                month: date.getMonth(),
                day: date.getDay(),
                date: date.getDate()
            }
        },
        formatWeek (day) {
            switch (day) {                
                case 0:
                    return '日';
                case 1:
                    return '一'
                case 2:
                    return '二';
                case 3:
                    return '三'
                case 4:
                    return '四'
                case 5:
                    return '五'
                case 6:
                    return '六'
            }
        },
        //判断闰年
        isLeapYear (year) {
             return (year % 4 == 0) && (year % 100 != 0 || year % 400 == 0);
        },
        //根据日子计算星期
        calcWeekend (year,month,day) {
            return new Date(year,month,day).getDay();
        },
        //计算某年某月的天数
        calcDays (year,month) {
            const monthDay = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]
            if(this.isLeapYear(year)&&month===1)
                return 29
            else 
                return monthDay[month]
        },
        //计算上个月天数
        calcLastMonthDays (year,month) {
            if(month===0) {
                return this.calcDays(year-1,11)
            }else {
                return this.calcDays(year,month-1)
            }
        },
        //上月
        lastMonth () {
            if(this.nowDate.month===0) {
                this.nowDate.month = 11
                this.nowDate.year --
            }else {
                this.nowDate.month --
            }
        },
        //下月
        nextMonth () {
            if(this.nowDate.month===11) {
                this.nowDate.month = 0
                this.nowDate.year ++
            }else {
                this.nowDate.month ++
            }
        },
        //去年
        lastYear () {
            this.nowDate.year --
        },
        //下一年
        nextYear () {
            this.nowDate.year ++
        },
        //计算当月开始星期
        startWeek () {
            return this.calcWeekend(this.nowDate.year,this.nowDate.month,1)
        },
        //
        clickEvent (e) {
            let monthNo = this.nowDate.month;
            let month = monthNo<=11?(monthNo+1):0
            let date = {
                year:this.nowDate.year,
                month:month,
                week: new Date(this.nowDate.year,this.nowDate.month,e.target.innerText).getDay(),
                day:Number(e.target.innerText)
            }
            this.$emit('click-event',date);
        } 
    }

}
</script>


<style>
    .calendar-container {
        padding: 2vw 3vw;
    }
    .year {
        text-align: center;
        margin-bottom: 10px;
        height: 30px;
    }
    .week,.date{
        box-sizing: border-box;
        display: flex;
        flex-wrap: wrap;
        list-style: none;
    }
    .week {
        border-bottom: .5px solid #ddd;
        margin-bottom: 5px;
    }
    ul>li {
        font-size: 14px;
        width: calc(94vw/7);
        height: 30px;
        text-align: center;
        line-height: 30px;
    }
    .none-week {
        color: #aaa;
    }
    .year>div {
        height: 30px;
        overflow: hidden;
    }
    .year span {
        line-height: 30px;
        font-size: 20px;
        display:  inline-block;
        width: 10%;
    }
    .year p {
        line-height: 2;
        width: 50%;
        display: inline-block;
    }
    .fl {
        float: left;
    }
    .fr {
        float: right;
    }
</style>

