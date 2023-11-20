<template>
    <div class="calendarbox">
        <div class="title">
            <div style="display: flex;">
                <div style="margin-right: 10px;" @click="yearbox_show=!yearbox_show" class="selectbox">
                    {{ year }}年 <span class="opt"></span>
                    <ul v-show="yearbox_show" class="optionbox">
                        <li :class="year_now+(y-3)===year?'select':''" @click.stop="selectYear(year_now+(y-3))" v-for="y in 10" :key="y">{{year_now+(y-3)}}年</li>
                    </ul>
                </div>
                <div @click="monthbox_show=!monthbox_show" class="selectbox">
                    {{ month }}月 <span class="opt"></span>
                    <ul v-show="monthbox_show" class="optionbox">
                        <li :class="m===month?'select':''" @click.stop="selectMonth(m)" v-for="m in 12" :key="m">{{m}}月</li>
                    </ul>
                </div>
            </div>
            <span @click="toToday" v-if="year_now+''+month_now!==year+''+month" class="totoday">返回今天</span>
        </div>
        <table class="weekbox">
            <thead>
                <tr>
                    <th class="week">一</th>
                    <th class="week">二</th>
                    <th class="week">三</th>
                    <th class="week">四</th>
                    <th class="week">五</th>
                    <th class="week">六</th>
                    <th class="week">日</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="r in 6" :key="r">
                    <td v-for="o in 7" :class="getClassName((r-1)*7+o-week+1)" :key="o" @click="choiceDate((r-1)*7+o-week+1)">
                        <template v-if="new Date(year,month-1,(r-1)*7+o-week+1).getMonth()===month-1">
                            <div class="datebox">
                                <p>{{ new Date(year,month-1,(r-1)*7+o-week+1).getDate() }}</p>
                                <p class="lunardate" :class="calendar.solar2lunar(year,month,(r-1)*7+o-week+1).lunarFestival||calendar.solar2lunar(year,month,(r-1)*7+o-week+1).festival||calendar.solar2lunar(year,month,(r-1)*7+o-week+1).Term?'festival':''">{{ calendar.solar2lunar(year,month,(r-1)*7+o-week+1).lunarFestival?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).lunarFestival:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).festival?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).festival:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).Term?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).Term:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).IDayCn==='初一'?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).IMonthCn:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).IDayCn }}</p>
                            </div>
                        </template>
                        <template v-else>
                            <div class="datebox notnowmonth">
                                <p>{{ new Date(year,month-1,(r-1)*7+o-week+1).getDate() }}</p>
                                <p class="lunardate" :class="calendar.solar2lunar(year,month,(r-1)*7+o-week+1).lunarFestival||calendar.solar2lunar(year,month,(r-1)*7+o-week+1).festival||calendar.solar2lunar(year,month,(r-1)*7+o-week+1).Term?'festival':''">{{ calendar.solar2lunar(year,month,(r-1)*7+o-week+1).lunarFestival?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).lunarFestival:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).festival?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).festival:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).Term?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).Term:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).IDayCn==='初一'?calendar.solar2lunar(year,month,(r-1)*7+o-week+1).IMonthCn:calendar.solar2lunar(year,month,(r-1)*7+o-week+1).IDayCn }}</p>
                            </div>
                        </template>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
<script setup>
import {ref} from 'vue'
import calendar from '../utils/js-calendar-converter';
const yearbox_show = ref(false);
const monthbox_show = ref(false);
const date_now = new Date();
const year_now = date_now.getFullYear();
const month_now = date_now.getMonth()+1;
const day_now = date_now.getDate();
let week_now = new Date(year_now,month_now-1,1).getDay();
const selectDate = ref('2023-8-31');
if(week_now===0){
    week_now = 7
}
const year = ref(year_now);
const month = ref(month_now);
const week = ref(week_now);
const selectYear = (y)=>{
    year.value = y;
    new Date(year.value,month.value-1,1).getDay()===0?week.value = 7: week.value = new Date(year.value,month.value-1,1).getDay();
    yearbox_show.value = false;
}
const selectMonth = (m)=>{
    month.value = m;
    new Date(year.value,month.value-1,1).getDay()===0?week.value = 7: week.value = new Date(year.value,month.value-1,1).getDay();
    monthbox_show.value = false;
}
const toToday = ()=>{
    year.value = year_now;
    month.value = month_now;
    new Date(year.value,month.value-1,1).getDay()===0?week.value = 7: week.value = new Date(year.value,month.value-1,1).getDay();
}
const getClassName = (d)=>{
    let select = selectDate.value.split('-');
    if(new Date(year.value,month.value-1,d).getTime()===new Date(select[0],select[1]-1,select[2]).getTime()){
        return 'selectDate'
    }else if(new Date(year.value,month.value-1,d).getTime()===new Date(year_now,month_now-1,day_now).getTime()){
        return 'datenow'
    }else{
        return '';
    }
}
const choiceDate = (d)=>{
    const newdate = new Date(year.value,month.value-1,d);
    year.value = newdate.getFullYear();
    month.value = newdate.getMonth()+1;
    let date = newdate.getDate();
    selectDate.value = year.value+'-'+month.value+'-'+date;
}
</script>
<style scoped>
.calendarbox{
    border: 1px solid #dcdcdc;
    font-size: 14px;
}
.totoday{
    color: #0085F2;
    cursor: pointer;
    display: flex;
    align-items: center;
}
.calendarbox p{
    margin: 0;
    padding: 0;
}
.calendarbox .title{
    display: flex;
    justify-content: space-between;
    padding: 12px;
}
.calendarbox .selectbox{
    height: 32px;
    border: 1px solid #DCDCDC;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 12px;
    border-radius: 4px;
    position: relative;
    cursor: pointer;
}
.notnowmonth{
    opacity: .4;
}
.calendarbox .selectbox .opt{
    height: 0;
    width: 0;
    border: 5px solid #333333;
    border-left: 5px solid #FFFFFF;
    border-right: 5px solid #FFFFFF;
    border-bottom: none;
    margin-left: 5px;

}
.calendarbox .selectbox .optionbox{
    border: 1px solid #DCDCDC;
    position: absolute;
    left: 0;
    top: 32px;
    background-color: #FFFFFF;
    padding: 0;
    margin: 0;
    border-radius: 4px;
    z-index: 10;
}
.calendarbox .selectbox .optionbox li{
    height: 26px;
    line-height: 26px;
    outline: none;
    padding: 0 12px;
    list-style-type: none;
    margin: 0;
    color: #333333;
}
.calendarbox .selectbox .optionbox .select{
    background-color: #0085F2;
    color: #FFFFFF;
}
.calendarbox .selectbox .optionbox li:hover{
    background-color: #0085F2;
    color: #FFFFFF;
}
.calendarbox .lunardate{
    font-size: 12px;
}
.calendarbox .weekbox .week{
    width: 40px;
    height: 40px;
    text-align: center;
    line-height: 40px;
}
.calendarbox td{
    width: 50px;
    height: 50px;
    cursor: pointer;
}
.datebox{
    width: 50px;
    height: 50px;
    display: flex;
    flex-direction: column;
    justify-content: center;
}
.datenow .datebox{
    color: #0085F2;
}
.selectDate .datebox{
    border-radius: 50px;
    background-color: #0085F2;
    color: #FFFFFF;
}
.calendarbox .festival{
    color: #f65128;
}
</style>