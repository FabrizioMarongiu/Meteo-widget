<template>
  <div class="widget">
    
    <div class="degrees" >
        <div class="left">
            ciao
        </div>
        <div class="right">
            ciao
        </div>
    </div>
    <div class="hours">
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
    </div>
    <div class="days">
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
        <div class="slot">
            ciao
        </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
    name:'Widget',
    data(){
        return{
            city:'london',
            apiKey:'7e3f2de0ca0a9f26649cf8f9d03d24b8',
            api:'https://api.openweathermap.org/data/2.5/weather?',
            // apiDays:'https://api.openweathermap.org/data/2.5/forecast?',
            apiDays:'https://api.openweathermap.org/data/2.5/onecall?',
            lat:45.4,
            long:9.1,
            data:[],
            current:{},
            daily:[],
            hourly:[],
            
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
                // this.current= res.data.current;
                // this.hourly= res.data.hourly;
                // this.daily= res.data.daily;
                // console.log(this.current);
                // console.log(this.hourly);
                // console.log(this.daily);
                         this.getHours();

            })
            .catch((error) => {
                console.log(error, 'Error');
            });
            
        },
        getHours(){
        // console.log(this.data.current)
            this.current={
                            clouds : this.data.current.clouds,
                            weather: this.data.current.weather,
                            temp: this.data.current.temp,
                        };
            console.log(this.data.daily)
            // let temp = this.hourly;
            // this.hourly=[];
            for(let i=0; i<=4; i++){
                // console.log(temp[i])
                // this.hourly+=temp[i];
                
                this.hourly.push( {
                                    clouds: this.data.hourly[i].clouds,
                                    weather: this.data.hourly[i].weather,
                                    temp: this.data.hourly[i].temp,
                                });
                this.daily.push( {
                                    clouds: this.data.daily[i].clouds,
                                    weather: this.data.daily[i].weather,
                                    temp: this.data.daily[i].temp,
                                });
            }
            // console.log(this.daily);
            // console.log(this.hourly);
        },

    }
}
</script>

<style scoped lang="scss">

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
        background-color: dodgerblue;
        margin-bottom: 10px;
        display:flex;
        flex-direction:row;
        justify-content: space-around;
        align-items: center;
    }
    .left,
    .right{
        flex-basis: calc ( 100% / 2 );
    }
    .hours,
    .days{
        padding:10px;
    }
    .slot{
        flex-basis: calc ( 100% / 5 -  10%);
        
    }
    
}
</style>