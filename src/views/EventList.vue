<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <section class="event-container">
      <EventCard v-for="event in events" :event="event" :key="event.title" />
      <div class="pagination_container">
        <router-link
          :to="{ name: 'EventList', query: { page: page - 1 } }"
          rel="prev"
          v-if="page != 1"
        >
          Prev Page
        </router-link>
        <router-link
          :to="{ name: 'EventList', query: { page: page + 1 } }"
          rel="next"
          v-if="hasNextPage"
        >
          Next Page
        </router-link>
      </div>
    </section>
  </div>
</template>

<script>
// @ is an alias to /src
import EventCard from '@/components/EventCard.vue';
import EventService from '@/services/EventService.js';
import { watchEffect } from 'vue';
export default {
  name: 'Home',
  props: ['page'],
  components: {
    EventCard,
  },
  data() {
    return {
      events: null,
      totalEvents: 0,
    };
  },
  created() {
    watchEffect(() => {
      this.events = null;
      EventService.getEvents(2, this.page)
        .then((response) => {
          this.events = response.data;
          this.totalEvents = response.headers['x-total-count'];
        })
        .catch((error) => {
          console.log(error);
        });
    });
  },
  computed: {
    hasNextPage() {
      var totalPages = Math.ceil(this.totalEvents / 2);
      return this.page < totalPages;
    },
  },
};
</script>
<style scoped>
.pagination_container {
  display: flex;
  justify-content: space-between;
  width: 290px;
}
.event-container {
  width: 100vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
</style>
