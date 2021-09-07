<template>
  <div id="app">
    <h1 class="site-title">
      {{ title }}
    </h1>
    <span class="site-description">{{ currentDate }}</span>

    <!--entry list-->
    <ul class="entry-list">
      <li v-for="entry in filteredEntries" :key="entry.id" class="entry-item">
        <span class="entry-daytime"
          >{{ entry[0] }} Uhr - {{ entry[1].replaceAll("/", ". ") }}</span
        >
        <h3 class="entry-title">{{ entry[2] }}</h3>
        <span class="entry-description">{{ entry[3] }}</span>
      </li>

      <!-- <li class="entry-item">
        <span clawhitess="entry-daytime">18.08.2021</span>
        <h3 class="entry-title">Besuch</h3>
        <span class="entry-description">Interessenten besuchen uns</span>
      </li> -->
    </ul>

    <!--footer-->
    <footer class="footer">
      <img
        src="./assets/STZH_SEB_Logo.png"
        alt="Stadt Zürich Soziale Betriebe und Einrichtungen Logo"
      />
      <img src="./assets/Opportunity.png" alt="Opportunity Logo" />
      <img src="./assets/SAG_Logo_De.png" alt="SAG Logo" />
    </footer>
  </div>
</template>

<script>
import axios from "axios"; //import the object axios from the library of axios

export default {
  name: "App",
  data() {
    return {
      title: "Welcome to Opportunity",
      currentDate: "",
      gsheet_url:
        "https://sheets.googleapis.com/v4/spreadsheets/15_C4Rx7692L5WNy4y4DEksZKDM0HRjrcUuXVTMtElwI/values:batchGet?ranges=A1%3AE100&valueRenderOption=FORMATTED_VALUE&key=AIzaSyBesotaNgSaTUIhrSKjEaExdi-ksKInhoE",
      entries: [],
    };
  },
  computed: {
    //computed properties are like data properties, but with a method combined, it gets executed automatically, instead of calling a function explicitely
    filteredEntries() {
      return [...this.entries].slice(1); //... means something, 'slice' removes first item of array
    },
  },
  methods: {
    getData() {
      axios.get(this.gsheet_url).then((response) => {
        this.entries = response.data.valueRanges[0].values;
        console.log(response);
      });
    },
    updateCurrentDate() {
      let today = new Date();
      const date = `${today.getDate()}.${today.getMonth() +
        1}.${today.getFullYear()}`;
      this.currentDate = date;
    },
    refreshData() {
      this.updateCurrentDate();
      this.getData();
    },
  },
  mounted() {
    this.refreshData(); //get initial data and wait for next update
    setInterval(() => {
      this.refreshData();
    }, 1800000); //wait 30min (in milliseconds) for next update
  },
};
</script>

<style lang="scss">
$bkgd: #e8eff4;
$blue: #0f05a0;
$orange: #eb5e00;
$peach: #ffbfab;
$grey: #9aa7b1;
$dkblue: #2c3e50;

@import url("https://fonts.googleapis.com/css2?family=Inter:wght@500;900&display=swap");

body {
  background-color: $bkgd;
}

#app {
  font-family: "Inter", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: $dkblue;
  margin-top: 60px;
}

.site-title {
  font-size: 62px;
  font-weight: 900;
  margin: 80px 0 20px 0;
}

.site-description {
  font-size: 62px;
  color: $grey;
  font-weight: 500;
  margin: 0;
}

.entry-list {
  padding-left: 0;
}

.entry-item {
  padding: 35px 40px;
  margin: 40px 0;
  font-size: 28px;
  line-height: 1.3;
  list-style: none;
  background-color: $blue;
}
.entry-daytime {
  font-weight: 900;
  color: $orange;
}

.entry-title {
  font-size: inherit;
  font-weight: 900;
  color: $peach;
  margin: 0;
}

.entry-description {
  color: $peach;
}

.footer {
  display: flex;
  justify-content: space-between;
  box-sizing: border-box;
  margin-top: 200px;
  padding: 40px;
  background: #fff;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;

  img {
    height: 50px;
  }
}
</style>
