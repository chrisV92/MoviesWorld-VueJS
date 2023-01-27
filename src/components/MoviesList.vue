<template>
  <div class="container">
    <div class="row py-5">
      <div class="col-3 offset-2">

        Found {{ total }} Movies

      </div>
      <div class="col-6">
        <div class="row">
          <div class="col-4 offset-6">
            <select id="filter" v-model="filter" class="form-select" aria-label="Default select example">
              <option value="null" selected="">Filter By</option>
              <option value="likes">Likes</option>
              <option value="dislikes">Hates</option>
              <option value="created_at">Date</option>
            </select>
          </div>
        </div>
      </div>

    </div>
    <div class="row">
      <div class="col-8 offset-2">
        <h3>Movies List</h3>
      </div>
      <div class="col-8 offset-2">
        <div class="card  my-3 movie" v-for="(item) in list">
          <div class="card-body">
            <div class="row pt-3 pb-3">
              <div class="col-9">
                {{ item.title }}
              </div>
              <div class="col-3 text-end">

                <p class="small" v-if="filter === null"> Posted {{ moment(item.created_at).format('D/M/Y') }}</p>
                <p class="small" v-else> Posted {{item.created_at}}</p>

              </div>
            </div>
            <p class="h6"> {{ item.description }}</p>
            <div class="row pt-3">
              <div class="col-6">
                <a href="/like/1" class="nav-item text-success text-decoration-none">
                  <svg width="18px" class="svg-inline--fa fa-thumbs-up mx-2 text-success " aria-hidden="true"
                       focusable="false" data-prefix="fas" data-icon="thumbs-up" role="img"
                       xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" data-fa-i2svg="">
                    <path fill="currentColor"
                          d="M313.4 32.9c26 5.2 42.9 30.5 37.7 56.5l-2.3 11.4c-5.3 26.7-15.1 52.1-28.8 75.2H464c26.5 0 48 21.5 48 48c0 25.3-19.5 46-44.3 47.9c7.7 8.5 12.3 19.8 12.3 32.1c0 23.4-16.8 42.9-38.9 47.1c4.4 7.2 6.9 15.8 6.9 24.9c0 21.3-13.9 39.4-33.1 45.6c.7 3.3 1.1 6.8 1.1 10.4c0 26.5-21.5 48-48 48H294.5c-19 0-37.5-5.6-53.3-16.1l-38.5-25.7C176 420.4 160 390.4 160 358.3V320 272 247.1c0-29.2 13.3-56.7 36-75l7.4-5.9c26.5-21.2 44.6-51 51.2-84.2l2.3-11.4c5.2-26 30.5-42.9 56.5-37.7zM32 192H96c17.7 0 32 14.3 32 32V448c0 17.7-14.3 32-32 32H32c-17.7 0-32-14.3-32-32V224c0-17.7 14.3-32 32-32z"></path>
                  </svg><!-- <i class=" mx-2 fa-solid fa-thumbs-up text-success"></i> Font Awesome fontawesome.com -->
                  {{ item.likes }}
                </a> |
                <a href="/dislike/1" class="nav-item text-danger text-decoration-none">
                  <svg width="18px" class="svg-inline--fa fa-thumbs-down mx-2 text-danger" aria-hidden="true"
                       focusable="false" data-prefix="fas" data-icon="thumbs-down" role="img"
                       xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" data-fa-i2svg="">
                    <path fill="currentColor"
                          d="M313.4 479.1c26-5.2 42.9-30.5 37.7-56.5l-2.3-11.4c-5.3-26.7-15.1-52.1-28.8-75.2H464c26.5 0 48-21.5 48-48c0-25.3-19.5-46-44.3-47.9c7.7-8.5 12.3-19.8 12.3-32.1c0-23.4-16.8-42.9-38.9-47.1c4.4-7.3 6.9-15.8 6.9-24.9c0-21.3-13.9-39.4-33.1-45.6c.7-3.3 1.1-6.8 1.1-10.4c0-26.5-21.5-48-48-48H294.5c-19 0-37.5 5.6-53.3 16.1L202.7 73.8C176 91.6 160 121.6 160 153.7V192v48 24.9c0 29.2 13.3 56.7 36 75l7.4 5.9c26.5 21.2 44.6 51 51.2 84.2l2.3 11.4c5.2 26 30.5 42.9 56.5 37.7zM32 320H96c17.7 0 32-14.3 32-32V64c0-17.7-14.3-32-32-32H32C14.3 32 0 46.3 0 64V288c0 17.7 14.3 32 32 32z"></path>
                  </svg><!-- <i class="mx-2 fa-solid fa-thumbs-down text-danger"></i> Font Awesome fontawesome.com -->
                  {{ item.dislikes }}
                </a>
              </div>
              <div class="col-6 text-end">
                <p class="h6" v-if="filter === null"> Posted By: <a class="text-info" :href="'/author/'+ item.user_id"> {{ item.user }} </a></p>
                <p class="h6" v-else> Posted By: <a class="text-info" :href="'/author/'+ item.user_id"> {{ item.user_first_name }} {{item.user_last_name}} </a></p>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
import moment from "moment";

export default {
  data() {
    return {
      list: null,
      total: 0,
      filter: null
    }
  },
  computed: {
    moment: () => moment,
  },
  mounted() {
    this.fetchMovies();
  },
  watch: {
    filter: function (value){
      axios.get('http://movieworld-symfony.test/api/v1/filters?filter=' + value)
          .then(response => {
            this.list = response.data
          })
          .catch(function (error) {
            console.log(error);
          })
    }
  },
  methods: {
    fetchMovies: function () {
      axios.get('http://movieworld-symfony.test/api/v1/movies')
          .then(response => {
            this.list = response.data
            this.total = this.list.length
          })
          .catch(function (error) {
            console.log(error);
          })
    },
  }
}
</script>