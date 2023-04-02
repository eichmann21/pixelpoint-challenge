<template>
    <div>
        <h1 class="title">Event Name: {{this.$route.params.event_name}}</h1>
     <div class="info">
        <div class="info_img"><img class="picture" v-if="event.image[0]['dc:originalUrl']" :src="event.image[0]['dc:originalUrl']"/></div>
    <div class="info_data">
        <div class="info_data_event">
        <span class="info_title">Zum Event</span>
        <p>Von: {{ convert_date(event.startDate) }}</p>
        <p>Bis: {{ convert_date(event.endDate) }}</p>
        <div v-if="event['eventSchedule'].length!=1">Termine: 
            <p  v-for="date in event.eventSchedule">
                {{ convert_date(date.startDate)}} 
                
                {{ date.startTime }}-

                {{ date.endTime==true?date.endTime:calc_endTime(date.duration,date.startTime) }}
            
            </p>
        </div>
    </div>
    <p class="info_desc" v-html="event.description"></p>
    </div>
     </div>
     <div class="info_sub">
        <span class="location">Ort: {{ event.location[0]?.address?.streetAddress }} {{ event.location[0]?.address?.postalCode }}-{{ event.location[0]?.address?.addressLocality }}</span>
        <span class="performer" v-if="event.performer">Veranstalter: {{ event.performer[0].name }}</span> 
        <span class="organizer" v-if="event.organizer">Organisator: {{ event.organizer[0].name}}</span>
     </div>   
    </div>
</template>

<script>
import data from "../../data.json"
const api_url=data.API_URL
export default {
    layout:'event',

    async asyncData({params,redirect}){
        const events=await fetch(api_url).then((res)=>res.json())
        const filtered=events['@graph'].find(
            (el)=>
            el['@id']===params.event_id
        )
        if(filtered){
        return {
            event:filtered
        }
        }
    },
    methods:{
    convert_date(time){
      const date=new Date(time)
      const year = date.toLocaleString("default", { year: "numeric" });
      const month = date.toLocaleString("default", { month: "2-digit" });
      const day = date.toLocaleString("default", { day: "2-digit" });
      return day+"."+month+"."+year
    },
    calc_endTime(duration,startTime){
        if(duration){
        if(duration.includes("H")){
            var split=duration.split("T")
            var time=split[1].split("H")
            var startTime_splitted=startTime.split(":")
            var endTime=parseInt(startTime_splitted[0])+parseInt(time[0])
            return endTime+":"+startTime_splitted[1]
        }
        }
    }
  }
    

}
</script>
<style>
.title{
    margin: 5vw;
    text-align: center;
}
.picture{
    width:100%;

}
.info_img{
    width: 50%;
    margin: 10px;
    max-height: 15vw;

}
.info{
    display: inline-flex;
    width: 100%;
}
.info_data{
    width: 50%;
    margin: 10px;
}
.info_title{
    font-size: 2vw;
    font-weight: bold;
}
.info_data_event{
    background-color: #292d76;
    text-align: center;
    max-height: 15vw;
    border-radius: 10px;
    padding: 10px;
    color: white;
}
.info_sub{
    width: 100%;
    text-align: center;
    margin-top: 5vw;
    margin-bottom: 5vw;
}
.performer,.organizer,.location{
    font-weight: bold;
    padding: 10px;
}
.info_desc{
    margin-top: 4vw;
    font-size: 1vw;
}
</style>