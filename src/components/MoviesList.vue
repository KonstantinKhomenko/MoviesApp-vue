<template>
  <b-container>
    <h3 class="list-title">{{ listTitle }}</h3>

    <b-row>
      <template v-if="isExist">
        <b-col cols="3" v-for="(movie, key) in list" :key="key">
          <MovieItem
            :movie="movie"
            @mouseover.native="onMouseOver(movie.Poster)"
            @removeItem="onRemoveItem"
          />
        </b-col>
      </template>
      <template v-else>
        <div>Empty List</div>
      </template>
    </b-row>
  </b-container>
</template>

<script>
import MovieItem from './MovieItem';
import { mapActions, mapGetters } from 'vuex';

export default {
  name: 'MoviesList',

  props: {
    list: {
      type: Object,
      default: () => ({})
    }
  },

  components: {
    MovieItem
  },

  computed: {
    ...mapGetters('movies', ['isSearch']),

    isExist() {
      return Boolean(Object.keys(this.list).length);
    },

    listTitle() {
      return this.isSearch ? 'Search result' : 'IMDB Top 250';
    }
  },

  methods: {
    ...mapActions('movies', ['removeMovie']),
    ...mapActions(['showNotify']),
    onMouseOver(poster) {
      this.$emit('changePoster', poster);
    },

    async onRemoveItem({ id, title }) {
      const isConfirmed = await this.$bvModal.msgBoxConfirm(
        `Are you sure delete ${title}?`
      );

      if (isConfirmed) {
        this.removeMovie(id);
        this.showNotify({
          msg: 'Movie deleted successful',
          variant: 'success',
          title: 'Success'
        });
      }
    }
  }
};
</script>

<style scoped>
.list-title {
  font-size: 50px;
  margin-bottom: 30px;
  color: #fff;
}
</style>
