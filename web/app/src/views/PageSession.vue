<template>
  <div class="page page-session">
    <div class="back-button-wrapper">
      <a @click="$router.go(-1)" class="back"> <img src="../assets/back.svg" alt /> Back </a>
    </div>
    <div class="page-content" v-if="session">
      <!-- <span>{{id}}</span> -->
      <div class="session-title">{{ session.title }}</div>

      <div class="speakers-wrapper" v-if="session.speakers">
        <router-link class="speaker-wrapper" v-for="speaker in session.speakers" :key="speaker.id" :to="{ name: 'speaker', params: { id: speaker.id } }">
          <div class="avatar">
            <img :src="getSpeakerPhoto(speaker.id)" alt />
          </div>
          <p class="name">{{ speaker.name }}</p>
        </router-link>
      </div>

      <div class="descriptions-row">
        <div class="des-wrap" v-if="session.format">
          <label>
            <img src="../assets/icons/language.svg" alt />
          </label>
          <p>{{ session.format }}</p>
        </div>

        <div class="des-wrap" v-if="session.language">
          <label>
            <img src="../assets/icons/language.svg" alt />
          </label>
          <p>{{ session.language }}</p>
        </div>

        <div class="des-wrap">
          <label>
            <img src="../assets/icons/location.svg" alt />
          </label>
          <p>{{ session.room }}</p>
        </div>

        <div class="des-wrap">
          <label>
            <img src="../assets/icons/time.svg" alt />
          </label>
          <p>
            {{ getDay(session.startsAt) }} {{ time(session.startsAt) }} -
            {{ time(session.endsAt) }}
          </p>
        </div>

        <div class="des-wrap" v-if="session.level">
          <label>
            <img src="../assets/icons/level.svg" alt />
          </label>
          <p>{{ session.level }}</p>
        </div>

        <div class="des-wrap rate" v-if="user.status">
          <template v-if="checkSessionStatus">
            <router-link v-if="voted" :to="{ name: 'feedback', params: { id: id } }" class="rate rated">✅ Rated. Thanks!</router-link>
            <router-link v-else :to="{ name: 'feedback', params: { id: id } }" class="rate">Rate</router-link>
          </template>
          <template v-else
            >Session not started yet</template
          >
        </div>

        <div class="des-wrap rate meetup" v-else>
          <a href="/b/login" class="rate">Login with meetup to rate</a>
        </div>
      </div>

      <div class="description-text">
        <p v-html="session.description">{{ session.description }}</p>
      </div>
    </div>
    <div class="page-content" v-else>
      <p>loading session...</p>
      <a href="javascript:location.reload()" title="i'm not proud of this code. please send PR">
        is this taking too long? click here
      </a>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
import { time as timeHelper, getDay as getDayHelper } from "@/helpers";

export default {
  props: ["id"],
  mounted() { },
  methods: {
    ...mapActions(["FETCH_SESSIONS", "FETCH_SPEAKERS", "fetchVotes"]),
    getSpeakerPhoto: function (id) {
      if (this.speakers.length === 0) {
        this.FETCH_SPEAKERS();
      } else {
        let theSpeaker = this.speakers.find(speaker => speaker.id === id);

        if (typeof theSpeaker === "undefined") {
          return "";
        } else {
          return theSpeaker.profilePicture;
        }
      }
      // if (typeof this.theSpeaker !== 'undefined') {
      // } else {
      //   return '/img/sponsors/placeholder.png'
      // }
    },
    time: timeHelper,
    getDay: getDayHelper,
  },
  computed: {
    ...mapGetters({
      sessions: "getSessions",
      speakers: "getSpeakers",
      user: "getUser",
      getVotes: "getVotes",
    }),
    session: function () {
      if (typeof this.sessions == "undefined") {
        this.FETCH_SESSIONS();
      }

      let sessions = Object.assign({}, this.sessions);
      return Object.keys(sessions)
                   .map(key => sessions[key].sessions)
                   .flat()
                   .find(session => parseInt(session.id) === parseInt(this.id));
    },
    voted: function () {
      //   let allVoted = _.map(this.getVotes, "session_id");
      //   if (allVoted.indexOf(this.id) !== -1) {
      //     return true;
      //   }
      return false;
    },
    checkSessionStatus() {
      /**
       * @TODO
       * Alternative to moment
       */

      // let timeNow = moment()
      //   .format()
      //   .substr(0, 19);
      // let timeStart = this.session.startsAt;
      // let difference = moment(timeNow).diff(moment(timeStart), "minutes");
      // console.log(timeNow);
      // console.log(timeStart);
      // console.log(difference);
      // if (difference && difference > 0) {
      //   return true;
      // } else {
      //   return false;
      // }
      return true;
    },
  },
  watch: {},
  beforeMount() {
    if (this.$store.state.sessions.length === 0) {
      // console.error("no sessions found");
      //   this.fetchVotes();
      //   this.fetchSessions();
      //   this.fetchSpeakers();
    } else {
      // console.info("sessions found !");
    }
  },
};
</script>

<style lang="scss" scoped>
.page-session {
  display: grid;
  grid-template-areas:
    "back back"
    "session session"
    "footer footer";
  grid-template-columns: 100px 1fr;
  grid-template-rows: 70px auto;
  grid-auto-rows: auto;
  max-width: 900px;
  margin: 0 auto;
  width: 100%;
}
a.back {
  text-decoration: none;
  text-transform: uppercase;
  font-size: 14px;
}

.page-content {
  text-align: center;
  a {
    color: var(--color-blue);
  }
}
.back-button-wrapper {
  cursor: pointer;
  --backsize: 60px;
  // grid-area: back;
  text-align: left;
  // margin-top: 5px;
  transform: translateX(calc(var(--backsize) * -2)) translateY(calc(var(--backsize)));
  position: absolute;
  a {
    display: flex;
    align-items: center;
    height: var(--backsize);
    width: var(--backsize);
    padding: calc(var(--backsize) / 4);
    // background: var(--color-blue);
    // color: var(--color-red);
    color: white;
    background: rgba($color-main, 0.5);
    border-radius: 100%;
    border: 3px solid #ff4932;
    padding-left: calc(var(--backsize) / 3.5);
    text-align: center;
    transition: transform 0.2s ease-in-out;
    // box-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
    img {
      height: 100%;
      // margin-right: 15px;
    }
  }
  &:hover {
    a {
      transform: translateX(-5px);
      transition: transform 0.2s ease-in-out;
    }
  }
}
@media screen and (max-width: 1000px) {
  .back-button-wrapper {
    position: static;
    grid-area: back;
    transform: unset;
    padding: unset;
    a {
      width: auto;
      border-radius: unset;
      // padding-left: ;
      border: none;
      background: white;
      color: var(--color-blue);
    }
  }
}
.page-content {
  grid-area: session;
  background: rgba(255, 255, 255, 0.9);
  box-shadow: 0 0 20px rgba(0, 0, 0, 1);
  border-radius: 15px;
}
.session-title {
  color: #333333;
  text-transform: uppercase;
  font-family: var(--font-glacial);
  font-size: 40px;
  font-weight: 700;
  margin: 0 auto;
  padding: 30px 5vw;
  text-align: center;
  // background: var(--color-blue);
  // color: white;
}
.speakers-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
  .speaker-wrapper {
    --width: 100px;
    display: grid;
    width: 200px;
    grid-template-areas: "avatar name";
    grid-template-columns: var(--width) 1fr;
    align-items: center;
    grid-gap: 10px;
    margin-bottom: 10px;
    margin-right: 20px;
    font-family: var(--font-shentox);
    font-weight: 500;
    text-transform: uppercase;
    &:last-child {
      margin-right: 0;
    }
    .avatar {
      grid-area: avatar;
      width: var(--width);
      height: var(--width);
      border-radius: var(--width);
      box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
      overflow: hidden;
      img {
        width: var(--width);
        height: var(--width);
        border-radius: var(--width);
        display: block;
        object-position: 50% 50%;
        object-fit: cover;
      }
    }
    p {
      grid-area: name;
      text-align: left;
      font-size: 15px;
      color: var(--color-blue);
      font-weight: 700;
    }
  }
}
.descriptions-row {
  background: var(--color-blue);
  width: 100%;
  display: flex;
  align-items: center;
  font-size: 18px;
  .des-wrap {
    width: 33.3%;
    margin-right: 10px;
    color: white;
    font-family: var(--font-glacial);
    text-transform: uppercase;
    display: flex;
    align-items: center;
    padding: 10px;
    height: 50px;
    &:last-child {
      margin-right: 0;
    }
    &.rate {
      padding: 0;
      a {
        background: var(--color-green);
        height: 50px;
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        text-decoration: none;
        font-size: 20px;
        color: var(--color-blue);
      }
    }
    &.meetup {
      a {
        background: var(--color-red-light);
        font-size: 14px;
        color: white;
      }
    }
    a.rated {
      background: black;
      color: var(--color-green);
    }
    label {
      margin-right: 10px;
      img {
        width: 20px;
      }
    }
    p {
      margin: 0;
    }
  }
}
.description-text {
  padding: 20px 30px;
  .back {
  }
  p {
    font-family: var(--font-glacial);
    font-size: 18px;
    line-height: 25px;
    font-weight: 300;
    white-space: pre-wrap;
    text-align: left;
  }
}
.button-wrappers {
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: 1fr;
  .back {
    grid-column: 1 / 2;
  }
}
.footer {
  grid-area: footer;
  color: black;
  text-transform: uppercase;
  font-family: var(--font-shentox);
  color: white;
  text-align: center;
  font-size: 15px;
  align-self: center;
  padding: var(--gutter) 0;
}
@media (max-width: 1000px) {
  .back-button-wrapper {
    // padding: 0 10px;
  }
  .session-title {
    font-size: 30px;
  }
}
@media (max-width: 768px) {
  .descriptions-row {
    flex-wrap: wrap;
    .des-wrap {
      width: 100%;
    }
  }
  .speakers-wrapper {
    flex-wrap: wrap;
    .speaker-wrapper {
      --width: 40px;
      width: 100%;
      margin-right: 0;
      padding: 0 10px;
      .name {
        font-size: 13px;
      }
    }
  }
}
@media (max-width: 480px) {
  .session-title {
    font-size: 25px;
  }
}
</style>
