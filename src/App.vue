<template lang="pug">
  #app
    header
      .container.py-5.d-flex.justify-content-center
          .row
              .col-md-4
                  .header__title.text-center.font-weight-bold 空氣品質指標 (AQI)
                  select#header__select.mt-2.border.font-weight-bold(v-model="selectCounty")
                    option(value="" hidden) 請選擇地區
                    option(:value="item" v-for="(item, key) in filterData" :key="item") {{item}}
              .col-md-8.text-center
                  .header__standard.font-weight-bold
                      .header__standard-box
                          .header__standard-value.border-bottom.bg-g 0~50
                          .header__standard-title 良好
                      .header__standard-box
                          .header__standard-value.border-bottom.bg-y 51~100
                          .header__standard-title 普通
                      .header__standard-box
                          .header__standard-value.border-bottom.bg-o 101~150
                          .header__standard-title.header__standard-title-s 對敏感族群不健康
                      .header__standard-box
                          .header__standard-value.border-bottom.bg-r 151~200
                          .header__standard-title.header__standard-title-s 對所有族群不健康
                      .header__standard-box
                          .header__standard-value.border-bottom.bg-p 201~300
                          .header__standard-title.header__standard-title-s.mt-4 非常不健康
                      .header__standard-box.border-right
                          .header__standard-value.border-bottom.bg-dr 301~400
                          .header__standard-title 危害
    section
      .container.py-6.d-flex.justify-content-center.align-items-center
          .city.font-weight-bold {{selectCounty}}
          .dash.ml-4.mr-5
          .time.font-weight-bold {{publishTime}} 更新
    section
      .container.py-5
        .row.no-gutters
          .col-md-4.d-flex.justify-content-end
              .city__info-f
                  .city__top__info.d-flex.text-center.font-weight-bold
                    .city__name-f.border.border-right-0.pt-10.text-truncate {{singleTown.SiteName}}
                    .city__AQI-f.border.py-11(:class="selectColor(singleTown.Status)"
                    v-if="singleTown.AQI") {{singleTown.AQI}}
                    .city__AQI.border.py-11(:class="selectColor(singleTown.Status)" v-else) N/A
                  .city__bottom__info.pt-9.border.border-top-0.mb-8
                      .city__info-list.d-flex.align-items-center.py-7
                        .city__info-name.font-weight-bold.mr-2 臭氧
                        .city__info-on.mr-auto O3 (ppb)
                        .city__info-num.font-weight-bold {{singleTown.O3}}
                      .city__info-list.d-flex.align-items-center.py-7
                        .city__info-name.font-weight-bold.mr-2 懸浮微粒
                        .city__info-on.mr-auto PM10 (μg/m³)
                        .city__info-num.font-weight-bold {{singleTown.PM10}}
                      .city__info-list.d-flex.align-items-center.py-7
                        .city__info-name.font-weight-bold.mr-2 細懸浮微粒
                        .city__info-on.mr-auto PM2.5 (μg/m³)
                        .city__info-num.font-weight-bold {{singleTown["PM2.5"]}}
                      .city__info-list.d-flex.align-items-center.py-7
                        .city__info-name.font-weight-bold.mr-2 一氧化碳
                        .city__info-on.mr-auto CO (ppm)
                        .city__info-num.font-weight-bold {{singleTown.CO}}
                      .city__info-list.d-flex.align-items-center.py-7
                        .city__info-name.font-weight-bold.mr-2 二氧化硫
                        .city__info-on.mr-auto SO2 (ppb)
                        .city__info-num.font-weight-bold {{singleTown.SO2}}
                      .city__info-list.d-flex.align-items-center.py-7.mb-8.border-0
                        .city__info-name.font-weight-bold.mr-2 二氧化氮
                        .city__info-on.mr-auto NO2 (ppb)
                        .city__info-num.font-weight-bold {{singleTown.NO2}}
          .col-md-8
            .ml-8.d-flex.flex-wrap.justify-content-start
              .city__info.mb-6.mr-8(v-for="(item, key) in getSeveral"
              :key="item.SiteName" @click="chooseInfo(item)")
                  .city__top__info.d-flex.text-center.font-weight-bold
                      .city__name.border.border-right-0.py-7.text-truncate {{item.SiteName}}
                      .city__AQI.border.py-7(:class="selectColor(item.Status)"
                      v-if="item.AQI") {{item.AQI}}
                      .city__AQI.border.py-7(:class="selectColor(item.Status)" v-else) N/A
</template>

<script>

export default {
  data() {
    return {
      items: [],
      counties: [],
      selectCounty: '',
      publishTime: '',
      singleTown: {},
      severalTown: [],
    };
  },
  methods: {
    getCounty() {
      const vm = this;
      const api = 'https://script.google.com/macros/s/AKfycbxoxEKXg7wfRCog0CU1Xfjq1FG3Z5r73T27qyITfqNKD9f5_Iir/exec?url=http://opendata.epa.gov.tw/webapi/Data/REWIQA/?format=json';
      this.$http({
        method: 'get',
        url: api,
      }).then((res) => {
        vm.items = res.data;
        vm.singleTown = vm.getTown(vm.selectCounty);
        // console.log(vm.items);
      });
    },
    getTown(town = '高雄市') {
      return this.items.find(item => item.County === town);
    },
    selectColor(status) {
      let cn = '';
      switch (status) {
        case '良好':
          cn = 'bg-g';
          return cn;
        case '普通':
          cn = 'bg-y';
          return cn;
        case '對敏感族群不健康':
          cn = 'bg-o';
          return cn;
        case '對所有族群不健康':
          cn = 'bg-r';
          return cn;
        case '非常不健康':
          cn = 'bg-p';
          return cn;
        case '危害':
          cn = 'bg-dr';
          return cn;
        default:
          cn = '';
          return cn;
      }
    },
    chooseInfo(item) {
      this.singleTown = item;
    },
  },
  computed: {
    filterData() {
      const vm = this;
      const town = new Set();
      vm.items.forEach((item) => {
        town.add(item.County);
        vm.publishTime = item.PublishTime;
      });
      vm.counties = Array.from(town);
      return vm.counties;
    },
    getSeveral() {
      /* eslint-disable */      
      return this.severalTown = this.items.filter((item) => {
        if (this.selectCounty) {
          return item.County === this.selectCounty;
        }
      });
    },
  },
  created() {
    this.getCounty();
    if (this.selectCounty === '') {
      this.selectCounty = '高雄市';
    }
  },
  watch: {
    /* eslint-disable */
    'selectCounty': 'getCounty',
  },
};
</script>

<style lang="scss">
  @import './assets/all';
</style>
