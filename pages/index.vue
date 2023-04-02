<template>
  <div>
    <h1 class="page_title">Alle Veranstaltungen</h1>
    <nuxt keep-alive :keep-alive-props="{ max: 10 }" />
    <p v-if="$fetchState.pending">Bitte Warten...</p>
    <p v-else-if="$fetchState.error">An error occurred :(</p>
    <div v-else >
      <div v-for="location in all_locations">
      {{ filter_location(location.location[0]?.address?.addressLocality) }}
      </div>
      <div class="filter_locations" ><h2>Veranstaltungen nach Location filtern</h2>
        <div class="filter_checkboxes" v-for="(location, index) in filter_locations_data" :key="index">
        <input type="checkbox" v-model="set_location" :id="index" :name="location" :value="location">
        <label :for="location">{{ location }}</label>
        </div>
        <br clear="all">
      </div>
      <div class="filter_locations" ><h2>Veranstaltungen nach Monat filtern</h2>
        <div class="filter_checkboxes">
        <input type="checkbox" v-model="find_date" id="01" name="Jänner" value="01">
        <label for="01">Jänner</label>
        <input type="checkbox" v-model="find_date" id="02" name="Februar" value="02">
        <label for="01">Februar</label>
        <input type="checkbox" v-model="find_date" id="03" name="März" value="03">
        <label for="01">März</label>
        <input type="checkbox" v-model="find_date" id="04" name="April" value="04">
        <label for="01">April</label>
        <input type="checkbox" v-model="find_date" id="05" name="Mai" value="05">
        <label for="01">Mai</label>
        <input type="checkbox" v-model="find_date" id="06" name="Juni" value="06">
        <label for="01">Juni</label>
        <input type="checkbox" v-model="find_date" id="07" name="Juli" value="07">
        <label for="01">Juli</label>
        <input type="checkbox" v-model="find_date" id="08" name="August" value="08">
        <label for="01">August</label>
        <input type="checkbox" v-model="find_date" id="09" name="September" value="09">
        <label for="01">September</label>
        <input type="checkbox" v-model="find_date" id="10" name="Oktober" value="10">
        <label for="01">Oktober</label>
        <input type="checkbox" v-model="find_date" id="11" name="November" value="11">
        <label for="01">November</label>
        <input type="checkbox" v-model="find_date" id="12" name="Dezember" value="12">
        <label for="01">Dezember</label>
      </div>
      </div>
      <br>
      <div>
        <div class="card" v-for="event in filtering">
        <a :href="`/${event['@id']}/${event.name}`">
        <h2 v-if="event['name'].length>30" class="card_heading">{{event['name'].substring(0,30)+"..."}}</h2>
        <h2 v-else class="card_heading">{{event['name']}}</h2>
      <!-- <img :src="event.image[0]['dc:originalUrl']" width="50" height="50"/> -->
      <p>Von: {{ convert_date(event.startDate) }} - Bis: {{ convert_date(event.endDate) }}</p>
      <p class="card_location">{{ event.location[0]?.address?.addressLocality||"Keine Angabe" }}<br>{{ event.location[0]?.address?.streetAddress||"" }}</p> 
      <br>  
      <p>Veranstaltungstyp: {{ event.location[0]['@type'][1].includes(":")? event.location[0]['@type'][1].split(':')[1] :event.location[0]['@type'][1] }}</p>
    </a> 
    </div> 
      </div>
    </div>
  </div>
</template>

<script>
import data from "../data.json"
const api_url=data.API_URL

export default{
  layout: 'default',
  data(){
    return{
      events:[],
      all_locations:[],
      filter_locations_data:[],
      set_location:[],
      find_date:[]
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
  activated() {
      // Call fetch again if last fetch more than 30 sec ago
      if (this.$fetchState.timestamp <= Date.now() - 30000) {
        this.$fetch()
      }
  },
  async fetch(){
    
    this.events=await fetch(api_url)
    .then(result=>result.json())
    .then(result=>result['@graph'])

    this.all_locations=await fetch(api_url)
    .then(result=>result.json())
    .then(result=>result['@graph'])
  }
  ,
  methods:{
    convert_date(time){
      if(time){
        const date=new Date(time)
      const year = date.toLocaleString("default", { year: "numeric" });
      const month = date.toLocaleString("default", { month: "2-digit" });
      const day = date.toLocaleString("default", { day: "2-digit" });
      return day+"."+month+"."+year
      }

    },
    filter_location(check_element){
      if(check_element){
        if(this.filter_locations_data.includes(check_element)!=true){
        this.filter_locations_data.push(check_element)
      }
      }
    },
  },
  computed:{
    filtering(){
      console.log(this.find_date.length)
      if(this.set_location.length!=0){
        var filtered_array=[]
      const filtered=this.events.find(
            (el,index)=>{
              if(this.set_location.includes(el.location[0]?.address?.addressLocality)){
                filtered_array.push(el)
              }
            })
        if(filtered_array){
          console.log(filtered_array)
          return filtered_array

        }
      }else if(this.find_date.length!=0){
        console.log(this.find_date)
        var filtered_array=[]
      const filtered=this.events.find(
            (el,index)=>{
              const date=new Date(el['startDate'])
              const month = date.toLocaleString("default", { month: "2-digit" });
              console.log(month)
              if(this.find_date.includes(month)){
                filtered_array.push(el)
              }
            })
        if(filtered_array){
          console.log(filtered_array)
          return filtered_array

        }
      }else{
        return this.events
      }

    }
  },
  loadingIndicator: {
    name: 'circle',
    color: '#3B8070',
    background: 'white'
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
<style>

.content{
  padding: 10px;
  text-align: center;
}
.page_title{
  margin: 5vw;
  font-size: 4vw;
}
.card{
  border-radius: 10px;
  -webkit-box-shadow: 5px 5px 14px -6px #292d76; 
  box-shadow: 5px 5px 14px -6px #292d76;
  width: 25vw;
  display: inline-block;
  margin: 10px;
  padding: 10px;
  text-align: center;
  max-height: 25vw;
}
.card:hover{
  background-color: #292d764f;
  cursor: pointer;
}
.card_heading{
  font-size: 1.5vw;
  max-height: 1.5vw;
  padding: 15px;
  padding-bottom: 20px;
  color: #292d76;
}
.card_location{
  max-height: 1vw;
}

a{
  color: black;
  text-decoration: none;
}
.filter_locations{
  margin: 2vw;
  margin-bottom: 5vw;
  border-radius: 10px;
  border-bottom: 1px black;
  /* -webkit-box-shadow: 5px 5px 14px -6px #000000; 
  box-shadow: 5px 5px 14px -6px #000000; */
  background-color: #292d76;
  display: inline-block;
  padding: 10px;
  color: white;
}
.filter_checkboxes{
float: left;
padding: 10px;
padding-right: 15px;
}

input[type="checkbox"] {

  width: calc(25px - 6px);
  height: calc(25px - 6px);
  border-radius: 5px;
  left: 3px;
  top: 3px;
  background-color: #e6e6e6;

}

a:active{
  color: black;
}


</style>
