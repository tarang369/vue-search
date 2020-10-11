<template>
  <div class="home">
    <h3 class="header">Player List</h3>
    <input
      type="text"
      placeholder="Search..."
      v-model="searchField"
      @change="searchPlayer"
    />
    <div class="grid">
      <div class="player" v-for="player in searchPlayer" :key="player.Id">
        <img :src="photoUrl(player.Id)" />
        <div class="player__info">
          Name : {{ player.PFName }}
          <br />
          Position : {{ player.SkillDesc }}
          <br />
          Market Value : ${{ player.Value }}m
          <br />
        </div>
        <div class="player__schedule">
          <span>
            Upcoming Match
          </span>
          <br />
          <!-- Added index of 0 because there is was not much data to loop through -->
          {{ player.UpComingMatchesList[0].CCode }}
          VS
          {{ player.UpComingMatchesList[0].VsCCode }}
          <br />
          Date: {{ localDate(player.UpComingMatchesList[0].MDate) }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      searchField: '',
      playerData: [],
    };
  },
  beforeMount() {
    this.getName();
  },
  methods: {
    async getName() {
      const res = await fetch(
        'https://api.jsonbin.io/b/5d0c6e6a860ae0341876aac6/2'
      );
      const data = await res.json();
      data.sort((a, b) => Number(a.Value) - Number(b.Value));
      this.playerData = data;
    },
    photoUrl(filename) {
      let images = require.context('../assets/player-images/', false, /\.jpg$/);
      return images('./' + filename + '.jpg');
    },
    localDate(fixture) {
      let UtcZone = new Date(`${fixture} UTC`);
      return UtcZone.toLocaleString();
    },
  },
  computed: {
    searchPlayer: function() {
      return this.playerData.filter(player =>
        player.PFName.toLowerCase().includes(this.searchField.toLowerCase())
      );
    },
  },
};
</script>

<style lang="scss" scoped>
.header {
  margin-top: 20px;
}

input {
  width: 350px;
  height: 30px;
  margin: 20px;
  padding: 0 10px;
}
.grid {
  padding: 10px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 10px;
  .player {
    border: 1px solid steelblue;
    line-height: 25px;
    font-weight: bold;
    &__info {
      background-color: cornflowerblue;
      color: whitesmoke;
    }
    &__schedule {
      span {
        text-decoration: underline;
      }
      color: black;
    }
  }
}
</style>
