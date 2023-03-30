<template>
  <div>
    <p v-if="$fetchState.pending">Fetching data...</p>
    <p v-else-if="$fetchState.error">An error occurred :(</p>
    <h1>Veranstaltungen</h1>
    <div v-for="event in events['@graph']">
      <h2>{{event.name}}</h2>
      <img :src="event.image[0]['dc:originalUrl']"/>
      <p>Start: {{ convert_date(event.startDate) }}</p>
      <p>Ende: {{ convert_date(event.endDate) }}</p>     
      <div v-html="event.description"></div>
      <p>Veranstaltungstyp: {{ event.location[0]['@type'][1].includes(":")? event.location[0]['@type'][1].split(':')[1] :event.location[0]['@type'][1] }}</p>

    </div>
  </div>
</template>

<script>
import data from "../data.json"
const api_url=data.API_URL

export default{
  data(){
    return{
      events:[]
    }
  },
  head(){
    return{
      title: 'Pixelpoint Challenge',
      meta: [
        {
          hid:"description",
          name:"description",
          content:"Developed by N. Eichmann"
        }
      ]
    }
  },
  async fetch(){
    this.events=await fetch(api_url)
    .then(result=>result.json())
  },
  methods:{
    convert_date(time){
      const date=new Date(time)
      const year = date.toLocaleString("default", { year: "numeric" });
      const month = date.toLocaleString("default", { month: "2-digit" });
      const day = date.toLocaleString("default", { day: "2-digit" });
      return day+"."+month+"."+year
    }
  }
}
/*function convert_date(time){
const date=new Date(time)
const year = date.toLocaleString("default", { year: "numeric" });
const month = date.toLocaleString("default", { month: "2-digit" });
const day = date.toLocaleString("default", { day: "2-digit" });
return day+"."+month+"."+year
}*/

</script>
