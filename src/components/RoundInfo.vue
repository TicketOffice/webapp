<template lang='pug'>
  .round-info-wrapper
    .movie-info
      img.movie-cover(:src='movie.uri')
      .movie-description
        .movie-name {{ movie.name }}
        .movie-type 类型: {{ movie.type }}
        .round-begin-time 时长: {{ calcDuration(round.beginTime, round.endTime) }}
    .cinema-info
      .cinema-name {{ cinema.name }}
      .cinema-address 地址: {{ cinema.address }}
      .round-place 影厅: {{ round.place }}
    .round-info
      .round-price ￥ {{ round.price }}
      el-button(type='primary', :disabled='disabled', @click='handleConfirm') 提交订单
</template>

<script>
import Moment from 'moment'

export default {
  props: {
    round: Object,
    disabled: Boolean,
  },
  computed: {
    movie () {
      return (this.round && this.round.movie) || {}
    },
    cinema () {
      return (this.round && this.round.cinema) || {}
    },
  },
  methods: {
    handleConfirm () {
      this.$emit('confirm')
    },
    calcDuration (start, end) {
      const duration = Moment.duration(Moment(end) - Moment(start))
      return `${duration.hours()}:${duration.minutes()}`
    },
  },
}
</script>

<style scoped>
.round-info-wrapper {
  display: flex;
  flex-direction: row;
  height: 6rem;
  overflow: hidden;
}

.movie-info, .round-info {
  flex: none;
}

.cinema-info {
  flex: auto;
}

.movie-info {
  white-space: nowrap;
  overflow: hidden;
  height: 6rem;
}

.movie-info, .cinema-info, .round-info {
  padding: 0 1rem;
  font-size: small;
}

.movie-info, .movie-description {
  height: 100%;
  display: inline-block;
  vertical-align: top;
}

.movie-info, .cinema-info {
  border-right: solid 1px #99A9BF;
}

.movie-cover {
  height: 6rem;
  width: 4.5rem;
  background-color: #A1A0A0;
}

.movie-description {
  padding-left: 0.5rem;
}

.movie-name, .cinema-name {
  font-size: large;
}

.round-price {
  font-size: xx-large;
}

</style>
