<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission v-for="item in this.missions" :key="item.slug" :mission="item" :selected="this.mission_slug"
            @click="selectMission(item)" />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:450px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:794px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg style="visibility: hidden; position: absolute;" width="0" height="0" xmlns="http://www.w3.org/2000/svg"
    version="1.1">
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix in="blur" mode="matrix" values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5" result="goo" />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup_old.ogg" type="audio/ogg" />
  </audio>
  <Footer />
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "004",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "The Drop",
          "status": "success"
        },
        {
          "slug": "002",
          "name": "Daybreak",
          "status": "partial-success",
        },
        {
          "slug": "003",
          "name": "Thunderbolt",
          "status": "success",
        },
        {
          "slug": "004",
          "name": "Dustgrave",
          "status": "start",
        },
      ],
      "pilots": [
        {
          "callsign": "Cosmos",
          "alias": "Butch Ryder",
          "code": "42d90239-7417-4413-956c-3641cffe5a8d ",
          "corpro": "IPS-N",
          "frame": "Nelson",
          "mech": "Unregulated Lightning"
        },
        {
          "callsign": "Lighthouse",
          "alias": "Duke Ryder",
          "code": "3684bf58-99ea-4dd0-bad2-c108fa991528",
          "corpro": "Smith-Shimano Corporation",
          "frame": "Black Witch",
          "mech": "Beacon Guardian"
        },
        {
          "callsign": "Wrench",
          "alias": "Leo",
          "code": "623eda96-3cf4-41a0-b787-6b202567547d",
          "corpro": "IPS-N",
          "frame": "Blackbeard",
          "mech": "Titan"
        },
        {
          "callsign": "Diamond",
          "alias": "Leopold Lanoie",
          "code": "8aZbP-3WcR5-dfG2H-6iJmK4-7L1NqO9",
          "corpro": "IPS-N",
          "frame": "Zheng",
          "mech": "Reality Change"
        }
      ],
      "header": {
        "planet": "Havelburg",
        "year": "5014u",
        "system": "Sideralis",
        "gate": "Kokshaal",
        "ring": "Cascade",
        "headerTitle": "Dustgrave",
        "headerSubtitle": "",
        "subheaderTitle": "UNS Rio Grande",
        "subheaderSubtitle": "17th Paladins",
      },
      "options": {
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if (this.options.eventsMarkdownPerMission) {
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if (self.options.eventsMarkdownPerMission) {
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1702px;
  height: 910px;
  overflow: hidden;
  margin-right: 200px;
}
</style>
