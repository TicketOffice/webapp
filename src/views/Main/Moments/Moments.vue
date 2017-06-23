<template lang='pug'>
  .moments-page
    moment-list.moments-menu
      router-link(v-for='(moment, index) in moments', key='index', :to='{ name: "MomentSeats", params: { roundId: moment.id.roundId , seatX: moment.id.seatX , seatY: moment.id.seatY} }')
        moment-list-item(:moment='moment' , :friend='user')
    router-view.secondary-content
</template>

<script>
import MomentList from '@/components/MomentList'
import MomentListItem from '@/components/MomentListItem'
import {Users , Rounds , SelfMoments} from '@/apis/moment'

export default {
  components: {
    MomentList,
    MomentListItem,
  },
  data () {
    return {
      moments: [],
      user: null,
      friendList: [],
      roundsList: [],
    }
  },
  computed: {
    userId () {
      return this.$route.params.userId;
    },
  },
  methods: {
    requestMoments: (userId) => {
      return SelfMoments.get({uid: userId})
                    .then((data) => {
                      return {
                        moments: data.body,
                      }
                    })
    },
    requestUser: (momentList) => {
      return Users.get({uid: momentList[0].id.userId})
                  .then((data) => {
                    return {
                      moment: momentList,
                      user: data.body,
                    }
                  })
    },
    requestRounds: (momentsList) => {
      let promise = Promise.resolve;
      let requestObject = {
        list: momentsList,
        pos: 0,
        result: [],
      };
      for (var i = 0 ; i < momentsList.length ; i++) {
        promise = promise.then((rounds) => {
          if (!rounds) rounds = requestObject;
          return Rounds.get()
                       .then((data) => {
                         rounds.result = rounds.result.push(data.body);
                         rounds.pos += 1;
                         return rounds;
                       })
        })
      }
      promise = promise.then((rounds) => {
        return {
          roundsList: rounds.result,
        }
      })
      return promise;
    },
  },
  created () {
    let self = this;
    let userId = self.userId;

    self.requestMoments(userId)
        .then((data) => {
          return self.requestUser(data.moments);
        })
        .then((data) => {
          self.moments = data.moment;
          self.user = data.user;
        })

  },
}
</script>

<style>
.moments-page {
  display: flex;
  flex-direction: row;
  align-items: stretch;
  height: 100%;
}

.moments-menu {
  flex: none;
  width: 20rem;
}
</style>
