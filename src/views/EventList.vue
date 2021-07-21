<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <router-link 
    :to="{name:'EventList',query:{page: page-1}}"
    rel="prev"
    v-if="page != 1"
    >Prev Page</router-link>
    <router-link 
    :to="{name:'EventList',query:{page: page+1}}"
    rel="next"
    v-if="hasNextPage"
    >Next Page</router-link>
  </div>
</template>

<script>
// @ is an alias to /src
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from "@vue/runtime-core";
// import axios from 'axios'
export default {
  name: 'EventList',
  props:{
    page: {
      type: Number,
      required: true
    }
  },
  components: {
    EventCard // register it as a child component
  },
  data() {
    return {
      events: null,
      totalEvent:0 //<--Add this to store totalEvents
    }
  },
  created() {
    watchEffect(()=>{
      EventService.getEvents(2,this.page)
        .then((response) => {
          this.events = response.data
          this.totalEvent = response.headers['x-total-count'] //<--Store it
        })
        .catch((error) => {
          console.log(error)
        })
    })
  },
  computed:{
    hasNextPage(){
      //Firat, calculate total pages
      let totalPages = Math.ceil(this.totalEvent/2) //2 is event per page.
      //Then check to see if the current page is less than the total pages.
      return this.page < totalPages
    }
  }
}
</script>
<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
