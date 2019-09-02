<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>
          <b>受精卵解析ダッシュボード</b>
        </span>
        <!-- <span class="font-weight-light">メニューなど</span> -->
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn text href="https://worldwing.sharepoint.com/sites/imaizumi-subwork" target="_blank">
        <span class="mr-2">ダミーリンク</span>
      </v-btn>
    </v-app-bar>

    <v-content>
      <DishTable/>
    </v-content>
  </v-app>
</template>

<script>
import DishTable from "./components/DishTable";

export default {
  name: "App",

  components: {
    DishTable
  },
  data: () => ({
    //
  })
};
</script>
