<template>
  <div class="widget">
    
    <div class="degrees" >
        <div class="left">
            <span class="fs40">{{current.temp.toFixed()}}°</span>
            <span class="f-grey fs10">{{city}}</span>
        </div>
        <div class="right" :class="current.background">
            
            <Icon :icon="current.icons" width="100" />
            
        </div>
    </div>
    <div class="hours">
        <div class="slot"
        v-for="(hour, index) in hourly" :key="index">
            <span class="fs7 mb-5px bold">{{hour.temp.toFixed()}}°</span>
            <span class="mb-5px"><Icon :icon="hour.icons" color="lightgray" /></span>
            <span class="fs10 mb-5px bold">{{time + (index+1)}}:00</span>
            <span v-if="time > 12 " class="fs7 f-lightgrey mb-5px">{{pm}}</span>
            <span v-else class="fs7 f-lightgrey mb-5px">{{am}}</span>
        </div>
    </div>
    <div class="days">
        <div class="slot"
        v-for="(daily, index) in daily" :key="index">
            <span class="fs7 mb-5px bold">{{daily.temp.day.toFixed()}}°</span>
            <span class="mb-5px"><Icon :icon="daily.icons" color="lightgray" /></span>
            <span class="fs10 mb-5px bold">{{daily.day}}</span>
        </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import{ Icon }from "@iconify/vue2";

export default {
    name:'Widget',
    components:{
        Icon,
    },
    data(){
        return{
            city:'Milan, IT',
            apiKey:'7e3f2de0ca0a9f26649cf8f9d03d24b8',
            api:'https://api.openweathermap.org/data/2.5/weather?',
            apiDays:'https://api.openweathermap.org/data/2.5/onecall?',
            lat:45.4,
            long:9.1,
            data:[],
            current:{},
            daily:[],
            hourly:[],
            time:'',
            am: 'AM',
            pm: 'PM',
            // today:'',
            week: [
                'Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 
                ],
            icons:'',
            
        }
    },
    created(){
        this.getWeatherDays();
    },
    methods:{
        // getWeather(){
        //     axios
        //     .get(this.api +`q=${this.city}&units=metric&appid=${this.apiKey}`)
                
        //     .then((res) => {
        //         this.data= res.data;
        //         console.log(this.data);
        //     })
        //     .catch((error) => {
        //         console.log(error, 'Error');
        //     });
        // },
        // getWeatherDays(){
        //     axios
        //     .get(this.apiDays +`q=${this.city}&units=metric&appid=${this.apiKey}`)
                
        //     .then((res) => {
        //         this.dataDays= res.data.list;
        //         console.log(this.dataDays);
        //     })
        //     .catch((error) => {
        //         console.log(error, 'Error');
        //     });
        // },
        getWeatherDays(){
            axios
            .get(this.apiDays +`lat=${this.lat}&lon=${this.long}&units=metric&appid=${this.apiKey}`)
                
            .then((res) => {
                this.data=res.data;
               
                this.getHours();

            })
            .catch((error) => {
                console.log(error, 'Error');
            });
            
        },
        getHours(){
            // GET HOURS AND DAYS
            var dayjs = require ('dayjs');
            
             this.time = dayjs().hour();
            
             let day = dayjs().get('day');
             
             console.log(day)
             
            //  this.today = this.week[day - 1];
            //   console.log(this.today);
         
            // GET OBJECT
            this.current={
                clouds : this.data.current.clouds,
                weather: this.data.current.weather[0].main,
                temp: this.data.current.temp,
            };
            
            if(this.current.weather === 'Clear'){
                if(this.time < 21){
                    this.current = {
                        ...this.current,
                        icons: 'emojione-v1:sun',
                        background:'sun',
                    }
                }else{
                    this.current = {
                        ...this.current,
                        icons: 'emojione-v1:full-moon',
                        background:'moon',
                    }
                }
            }else if(this.current.weather === 'Clouds'){
                this.current = {
                        ...this.current,
                        icons: 'emojione-v1:cloud',
                        background:'cloud',
                    }
            }else if(this.current.weather === 'Rain'){
                this.current = {
                        ...this.current,
                        icons: 'emojione-v1:cloud-with-rain',
                        background:'rain',
                    }
            }else if(this.current.weather === 'Thunderstorm'){
                    this.current = {
                            ...this.current,
                            icons: 'icon-park:thunderstorm-one',
                            background:'thunderstorm',
                        }
            }
            
            // console.log(this.current);
            let nextDay = day + 1;

            for(let i=0; i<=4; i++){
                
                if(nextDay === 7){
                 nextDay = 0;
                }else{ 
                    nextDay = nextDay + 1;
                }
                console.log(nextDay)  
                let tempHourly ={
                                    clouds: this.data.hourly[i].clouds,
                                    weather: this.data.hourly[i].weather[0].main,
                                    temp: this.data.hourly[i].temp,
                                };
                if(tempHourly.weather === 'Clear'){
                        this.hourly.push( {
                            ...tempHourly,
                            icons: 'clarity:sun-line',
                        });
                }else if(tempHourly.weather === 'Clouds'){
                        this.hourly.push({
                            ...tempHourly,
                            icons: 'akar-icons:cloud',
                        });
                }else if(tempHourly.weather === 'Rain'){
                    this.hourly.push({
                            ...tempHourly,
                            icons: 'carbon:cloud-rain',
                        });
                }else if(tempHourly.weather === 'Thunderstorm'){
                    this.hourly.push({
                            ...tempHourly,
                            icons: 'ion:thunderstorm-outline',
                        });
                }
                

                let tempDaily =  {
                                    clouds: this.data.daily[i].clouds,
                                    weather: this.data.daily[i].weather[0].main,
                                    temp: this.data.daily[i].temp,
                                    day : this.week[nextDay],
                                };
                
                


                if(tempDaily.weather === 'Clear'){
                        this.daily.push( {
                            ...tempDaily,
                            icons: 'clarity:sun-line',
                        })
                }else if(tempDaily.weather === 'Clouds'){
                        this.daily.push({
                            ...tempDaily,
                            icons: 'akar-icons:cloud',
                        })
                }else if(tempDaily.weather === 'Rain'){
                        this.daily.push({
                            ...tempDaily,
                            icons: 'carbon:cloud-rain',
                        })
                }else if(tempDaily.weather === 'Thunderstorm'){
                    this.daily.push({
                            ...tempDaily,
                            icons: 'ion:thunderstorm-outline',
                        });
                }
            }
            

              console.log(this.current);

            //   if(this.daily[i].day && this.daily[i].day > 6){
                    
            //     }
            
            

        },

    }
}
</script>

<style scoped lang="scss">
@import '../styles/utilities.scss';

.widget{
    display:flex;
    flex-direction: column;
    width: 400px;
    height: auto;
    .degrees,
    .hours,
    .days{
        width: 100%;
        height: 150px;
        border-radius: 10px;
        background-color: #fff;
        margin-bottom: 10px;
        display:flex;
        flex-direction:row;
        justify-content: space-around;
        align-items: center;
        box-shadow: 8px 8px 3px rgba(211, 211, 211, 0.959);
    }
    .left,
    .right
    {
        display: flex;
        flex-basis: 50%;
        height: 100%;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        overflow: hidden;
        border-radius: 0 10px 10px 0;
    }
    .hours,
    .days{
        padding:10px;
    }
    .slot{
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }
    
    
    
}
</style>