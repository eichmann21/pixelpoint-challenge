<template>
    <div>
        <h1>Event Name: {{this.$route.params.event_name}}</h1>

    <img v-if="event.image[0]['dc:originalUrl']" :src="event.image[0]['dc:originalUrl']"/>
      <p>Von: {{ convert_date(event.startDate) }}</p>
      <p>Bis: {{ convert_date(event.endDate) }}</p>

      <div v-if="event['eventSchedule'].length!=1">Termine: 
        <p  v-for="date in event.eventSchedule">
            {{ convert_date(date.startDate)}} 
            
            {{ date.startTime }}-

            {{ date.endTime==true?date.endTime:calc_endTime(date.duration,date.startTime) }}
        
        </p>
    </div>
      <p v-html="event.description"></p>
      <p>Ort: {{ event.location[0]?.address?.streetAddress }}-{{ event.location[0]?.address?.postalCode }}-{{ event.location[0]?.address?.addressLocality }}</p>
      <p v-if="event.performer">Veranstalter: {{ event.performer[0].name }}</p> 
      <p v-if="event.organizer">Organisator: {{ event.organizer[0].name}}</p>
    </div>
</template>

<script>
import data from "../../data.json"
const api_url=data.API_URL
export default {

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

</script>