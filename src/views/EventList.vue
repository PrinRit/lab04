<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="pagination">
      <router-link 
      id="page-prev"
      :to="{name:'EventList',query:{page: page-1}}"
      rel="prev"
      v-if="page != 1"
      >Prev Page</router-link>
      <router-link 
      id="page-next"
      :to="{name:'EventList',query:{page: page+1}}"
      rel="next"
      v-if="hasNextPage"
      >Next Page</router-link>
    </div>

    <select id="rating" v-model.number="rating">
          <option>6</option>
          <option>5</option>
          <option>4</option>
          <option>3</option>
          <option>2</option>
          <option>1</option>
        </select> 
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
      totalEvent:0,//<--Add this to store totalEvents
      rating:1
    }
  },
  created() {
    watchEffect(()=>{
      EventService.getEvents(this.rating,this.page)
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
      let totalPages = Math.ceil(this.totalEvent/this.rating) //2 is event per page.
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
.pagination{
  display: flex;
  width: 290px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}

#page-prev{
  text-align: left;
}

#page-next{
  text-align: right;
}

#limits{
  text-align: center;
}
</style>
