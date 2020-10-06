<template>
    <div>

        <b-table :items="this.content"></b-table>

    </div>
</template>


<script>

  export default {
    name: 'bike',
    data() {
      return {
        content: Array,
      };
    },
    mounted() {

      fetch('https://api.digitransit.fi/routing/v1/routers/hsl/index/graphql', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({
          query: `{
            bikeRentalStations {
              stationId
              name
              bikesAvailable
              spacesAvailable
              lat
              lon
              allowDropoff
            }
          }`
        })
      }).then(res => res.json()).then(res => {
        this.content = res.data.bikeRentalStations;
      })
    },
  }


</script>

<style scoped>

</style>

