<template>
  <div class="widget">

    <carousel :perPageCustom="[[480, 1], [768, 1]]"  >
        <slide>
            <div class="degrees" >
                <div class="left">
                    <span class="fs40">{{current.temp.toFixed()}}°</span>
                    <span class="f-grey fs10">{{city}}</span>
                </div>
                <div class="right" :class="current.background">
                    <Icon :icon="current.icons" width="100" />
                </div>
            </div>
        </slide>
        <slide>
            <div class="hours">
                <div class="slot"
                v-for="(hour, index) in hourly" :key="index">
                    <span class="fs7 mb-5px bold">{{hour.temp.toFixed()}}°</span>
                    <span class="mb-5px"><Icon :icon="hour.icons" color="lightgray" /></span>
                    <span class="fs10 mb-5px bold">{{hour.hour}}:00</span>
                    <span v-if="time > 12 " class="fs7 f-lightgrey mb-5px">{{pm}}</span>
                    <span v-else class="fs7 f-lightgrey mb-5px">{{am}}</span>
                </div>
            </div>
        </slide>
        <slide>
             <div class="days">
                <div class="slot"
                v-for="(daily, index) in daily" :key="index">
                    <span class="fs7 mb-5px bold">{{daily.temp.day.toFixed()}}°</span>
                    <span class="mb-5px"><Icon :icon="daily.icons" color="lightgray" /></span>
                    <span class="fs10 mb-5px bold">{{daily.day}}</span>
                </div>
            </div>
        </slide>
    </carousel>

  </div>
</template>

<script>
import axios from "axios";
import{ Icon }from "@iconify/vue2";
import { Carousel, Slide } from 'vue-carousel';

export default {
    name:'Widget',
    components:{
        Icon,
        Carousel,
        Slide,
    },
    data(){
        return{
            apiKey:'7e3f2de0ca0a9f26649cf8f9d03d24b8',
            api:'https://api.openweathermap.org/data/2.5/onecall?',
            city:'Milan, IT',
            lat:45.4,
            long:9.1,
            data:[],
            current:{},
            daily:[],
            hourly:[],
            time:'',
            am: 'AM',
            pm: 'PM',
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
        getWeatherDays(){
            // API CALL
            axios
            .get(this.api +`lat=${this.lat}&lon=${this.long}&units=metric&appid=${this.apiKey}`)
                
            .then((res) => {
                this.data=res.data;
                this.settingTemp();
            })
            .catch((error) => {
                console.log(error, 'Error');
            });
            
        },
        settingTemp(){
            // GET HOURS AND DAYS
            var dayjs = require ('dayjs');
             this.time = dayjs().hour();
             let day = dayjs().get('day');
         
            // GET OBJECT CURRENT DAY
            this.current={
                clouds : this.data.current.clouds,
                weather: this.data.current.weather[0].main,
                temp: this.data.current.temp,
            };
            
            // SET ICONS AND BACKGROUND
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
            
            // SET NEXT HOURS
            let nextHour = this.time;

            // SET NEXT DAYS
            let nextDay = day + 1;

            for(let i=0; i<=4; i++){
                
                // GET OBJECTS OF HOURLY TEMPERATURE AND SETTING ICONS
                if(nextHour === 23){
                    nextHour = 0;
                }else{ 
                    nextHour = nextHour + 1;
                }

                let tempHourly ={
                                    clouds: this.data.hourly[i].clouds,
                                    weather: this.data.hourly[i].weather[0].main,
                                    temp: this.data.hourly[i].temp,
                                    hour: nextHour,
                                };
                
                this.setObject(tempHourly, this.hourly);

                // GET OBJECT OF DAILY TEMPERATURE AND SET ICONS
                if(nextDay === 7){
                    nextDay = 0;
                }else{ 
                    nextDay = nextDay + 1;
                }

                
                let tempDaily =  {
                                    clouds: this.data.daily[i].clouds,
                                    weather: this.data.daily[i].weather[0].main,
                                    temp: this.data.daily[i].temp,
                                    day : this.week[nextDay],
                                };
                this.setObject(tempDaily, this.daily);
                
            }       
        },
        setObject(temp, object){
            
            if(temp.weather === 'Clear'){
                    object.push( {
                        ...temp,
                        icons: 'clarity:sun-line',
                    })
            }else if(temp.weather === 'Clouds'){
                    object.push({
                        ...temp,
                        icons: 'akar-icons:cloud',
                    })
            }else if(temp.weather === 'Rain'){
                    object.push({
                        ...temp,
                        icons: 'carbon:cloud-rain',
                    })
            }else if(temp.weather === 'Thunderstorm'){
                object.push({
                        ...temp,
                        icons: 'ion:thunderstorm-outline',
                    });
            }
            
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
        display:flex;
        flex-direction:row;
        justify-content: space-around;
        align-items: center;
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
    .VueCarousel-slide {
        border-radius: 10px;
    }   
}

</style>