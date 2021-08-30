<template>
  <div class="movie-database pt-10">
    <div class="container mx-auto flex justify-center">
      <h1 class="font-bold text-2xl">Movie Database</h1>
    </div>

    <div class="container mx-auto px-4 sm:px-8">
      <div class="py-8">
        <div class="-mx-4 sm:-mx-8 px-4 sm:px-8 py-4 overflow-x-auto">
          <div class="inline-block min-w-full shadow rounded-lg overflow-hidden">
            <table class="min-w-full leading-normal">
              <thead>
              <tr>
                <th
                    class="px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                </th>
                <th v-on:click="sort('title')"
                    class="cursor-pointer px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                  Title
                  <i v-if="query.sort == 'title' && query.order == 'desc'" class="fas fa-caret-up"></i>
                  <i v-if="query.sort == 'title' && query.order == 'asc'" class="fas fa-caret-down"></i>
                </th>
                <th v-on:click="sort('release_date')"
                    class="cursor-pointer px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                  Release Date
                  <i v-if="query.sort == 'release_date' && query.order == 'desc'" class="fas fa-caret-up"></i>
                  <i v-if="query.sort == 'release_date' && query.order == 'asc'" class="fas fa-caret-down"></i>
                </th>
                <th v-on:click="sort('popularity')"
                    class="cursor-pointer px-5 py-3 border-b-2 border-gray-200 bg-gray-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                  Popularity
                  <i v-if="query.sort == 'popularity' && query.order == 'desc'" class="fas fa-caret-up"></i>
                  <i v-if="query.sort == 'popularity' && query.order == 'asc'" class="fas fa-caret-down"></i>
                </th>
              </tr>
              </thead>
              <tbody>
              <tr class="bg-white hover:bg-gray-300 cursor-pointer" v-for="movie in movies" :key="movie.id" @click="openLink(movie)">
                <td class="px-5 py-5 border-b border-gray-200 text-sm">
                  <div class="flex items-center">
                    <div class="flex-shrink-0 w-10 h-10">
                      <img v-if="movie.image !== ''" class="w-full h-full rounded-full"
                           :src="movie.image"
                           :alt="movie.title" />
                      <img v-if="movie.image === ''" class="w-full h-full rounded-full"
                           src="/default-movie.png"
                           alt="No Image Found" />
                    </div>
                  </div>
                </td>
                <td class="px-5 py-5 border-b border-gray-200 text-sm">
                  <div class="flex items-center">
                    <div class="ml-3">
                      <p class="text-gray-900 whitespace-no-wrap">
                        {{ movie.title }}
                      </p>
                    </div>
                  </div>
                </td>
                <td class="px-5 py-5 border-b border-gray-200 text-sm">
                  <p class="text-gray-900 whitespace-no-wrap">
                    {{ movie.release_date }}
                  </p>
                </td>
                <td class="px-5 py-5 border-b border-gray-200 text-sm">
                  <p class="text-gray-900 whitespace-no-wrap">
                    {{ movie.popularity }}
                  </p>
                </td>
              </tr>
              </tbody>
            </table>
            <div
                class="px-5 py-5 bg-white border-t flex flex-col xs:flex-row items-center xs:justify-between          ">
                        <span class="text-xs xs:text-sm text-gray-900">
                            Showing page {{ query.page }} of {{ totalPages }}
                        </span>
              <div class="inline-flex mt-2 xs:mt-0">
                <button v-if="query.page > 1" @click="prevPage()"
                    class="text-sm bg-gray-300 hover:bg-gray-400 text-gray-800 font-semibold py-2 px-4 rounded-l">
                  Prev
                </button>
                <button v-if="query.page < totalPages" @click="nextPage()"
                    class="text-sm bg-gray-300 hover:bg-gray-400 text-gray-800 font-semibold py-2 px-4 rounded-r">
                  Next
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'MovieDatabase',
  props: {
    msg: String
  },
  data() {
    return {
      movies: [],
      totalPages: 1,
      query: {
        sort: 'title',
        page: 1,
        order: 'asc',
      }
    };
  },
  mounted() {
    this.fetch()
  },
  watch: {
    query: {
      handler: function() {
        this.fetch();
      },
      deep: true
    }
  },
  methods: {
    sort(type) {
      this.query.page = 1;

      if (this.query.sort !== type) {
        this.query.sort = type;
        this.query.order = 'asc';
        return;
      }

      if (this.query.order === 'asc') {
        this.query.order = 'desc';
        return
      }

      this.query.order = 'asc';
    },
    nextPage() {
      console.log('hi');
      this.query.page = this.query.page + 1;
    },
    prevPage() {
      console.log('hi prev');
      if (this.query.page > 1) {
        this.query.page = this.query.page - 1;
      }
    },
    openLink(movie) {
      window.open(movie.link);
    },
    fetch() {
      axios.get(process.env.VUE_APP_API_URL + 'api/discover/movie', { params: this.query} )
      .then((results) => {
        console.log(results);
        this.movies = results.data.data;
        this.page = results.data.page;
        this.totalPages = results.data.total_pages;
      })
    }
  }
}
</script>