<template>
  <v-container grid-list-md>
    <h2>
      <b>ディッシュ一覧画面</b>
    </h2>
    <v-layout>
      <v-card xs12 sm8 offset-sm2 v-if="dispTable">
        <!-- デバッグ用 -->
        <!-- <div>{{tDishDatas}}</div> -->
        <div id="x_data_area">
          <!-- 固定したい部分 -->
          <table id="header" class="csTbl">
            <tbody>
              <tr>
                <th>
                  Dish
                  <br>number
                </th>
                <th>
                  Dish
                  <br>ID
                </th>
              </tr>
              <tr v-for="tDishData in tDishDatas">
                <th>{{tDishData.dish_no}}</th>
                <th>{{tDishData.dish_id}}</th>
              </tr>
            </tbody>
          </table>

          <!-- スクロールしたい部分 -->
          <div id="table-data">
            <table class="csTbl">
              <tbody>
                <tr>
                  <th v-for="n in width">
                    {{allDay[n-1]}}
                    <br>
                    {{allWeek[n-1]}}
                  </th>
                </tr>
                <tr v-for="tDishData in tDishDatas">
                  <td v-for="n in width">
                    <div v-if="tDishDatas[0].whenObs[n-1] == 0" id="red">D0</div>
                    <div
                      v-else-if="tDishDatas[0].whenObs[n-1] > 0"
                      id="green"
                    >D{{tDishDatas[0].whenObs[n-1]}}</div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </v-card>
    </v-layout>
  </v-container>
</template>



<script>
import axios from "axios";
import moment from "moment";
export default {
  name: "App",
  data() {
    return {
      dispTable: false,
      width: 60,
      allDay: [],
      allWeek: [],
      tDishDatas: ""
    };
  },
  async created() {
    try {
      await axios
        .get("http://localhost:8000")
        .then(res => {
          // DBから受け取ったjsonを格納
          this.tDishDatas = res.data.result.tDish;

          // 差を計算
          // this.tDishDatas[0].howLongDate = moment(
          //   this.tDishDatas[0]["end_observation_time"]
          // ).diff(moment(this.tDishDatas[0]["start_observation_time"]), "days");
          // this.tDishDatas[0].startMonse = Number(
          //   moment(this.tDishDatas[0]["start_observation_time"]).format("M")
          // );
          // this.tDishDatas[0].startDate = Number(
          //   moment(this.tDishDatas[0]["start_observation_time"]).format("D")
          // );
          // moment(this.tDishDatas[0]["start_observation_time"]).format(
          //   "YY/MM/DD"
          // );

          // htmlで使用するデザインを決定する配列を事前に作成
          for (var n = 0; n < this.tDishDatas.length; n++) {
            this.tDishDatas[n].whenObs = [];
            for (var k = 0; k < this.width; k++) {
              if (
                moment(this.tDishDatas[n]["end_observation_time"]).format(
                  "YY/MM/DD"
                ) ==
                moment()
                  .add(k, "days")
                  .format("YY/MM/DD")
              ) {
                // 観察終了日
                this.tDishDatas[n].whenObs[k] = 0;
              } else if (
                moment(this.tDishDatas[n]["end_observation_time"]).diff(
                  moment().add(k, "days"),
                  "days"
                ) > 0 &&
                moment(moment().add(k, "days")).diff(
                  moment(this.tDishDatas[n]["start_observation_time"]),
                  "days"
                ) >= 0
              ) {
                // 観察期間
                this.tDishDatas[n].whenObs[k] = moment(
                  this.tDishDatas[n]["end_observation_time"]
                ).diff(moment().add(k, "days"), "days");
              } else {
                // なんでもない日
                this.tDishDatas[n].whenObs[k] = -1;
              }
            }
          }

          for (var i = 0; i < this.width; i++) {
            this.allDay.push(
              moment()
                .add(i, "days")
                .format("M/D")
            );
            this.allWeek.push(
              moment()
                .add(i, "days")
                .format("(ddd)")
            );
          }
          // データを取得&処理が完了した段階で描写する
          this.dispTable = true;
        })

        .catch(e => {
          console.error(e);
        });
    } catch (e) {
      console.error(e);
    }
  }
};
</script>

<style>
table {
  border: 1px solid #000000;
  border-collapse: collapse;
  font-size: 13px;
}
td,
th {
  border: 1px solid #000000;
  padding: 2px;
  text-align: center;
  width: 20px;
}
table th {
  background: #f2f2f2;
  color: #000000;
}

#x_data_area {
  width: 1250px;
  position: relative;
}

/**
* 固定したい部分
*/

#header {
  width: 14%;
  position: absolute;
  left: 0px;
  top: 0px;
}

#table-data {
  width: 86%;
  position: absolute;
  left: 14%;
  top: 0;
  overflow-x: scroll;
}

#table-data table {
  width: 4000px;
}

#green {
  background: rgb(17, 131, 2); /* 背景を緑で塗る */
  color: #fff; /*文字を白くする */
  /*
  角丸の指定
  */
  border-radius: 3px;
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
}

#red {
  background: rgb(218, 47, 47);
  color: #fff;
  border-radius: 3px;
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
}
</style>
