<template id="tyontekijataulu">
    <div>
        <b-table :items="myJson"></b-table>
    </div>

</template>

<script>

  import usersData from '@/components/tyontekijat.json';

  export default {
    data() {
      return {
        myJson: usersData.tyontekijat
      };
    },
  };
</script>
