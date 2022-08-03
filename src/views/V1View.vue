<template lang="pug">
v-container
  v-card.pa-8
    v-card-title.pa-0 部屋の寸法
    v-text-field(
      v-model="room.height"
      label="高さ"
      suffix="cm"
      type="number"
    )
    v-text-field(
      v-model="room.widthY"
      label="幅 ( 縦 )"
      suffix="cm"
      type="number"
    )
    small.my-2 縦　面積（高さ × 縦幅）： {{ ( room.height * room.widthY ).toLocaleString() }} cm2
    v-text-field(
      v-model="room.widthX"
      label="幅 ( 横 )"
      suffix="cm"
      type="number"
    )
    small.my-2 横　面積（高さ × 横幅）： {{ ( room.height * room.widthX ).toLocaleString() }} cm2
    hr.mx-n8.my-8
    h4.mb-2 壁全面の面積 {{ ( (room.height * room.widthY + room.height * room.widthX) * 2 ).toLocaleString() }} cm2
    small 数式： (縦 面積　＋　横 面積) x 2
  v-card.pa-8.mt-4(color="blue lighten-4")
    v-card-title.pa-0 除外したい面積
    transition-group.d-flex.flex-wrap.justify-space-around(
      name="list" tag="div"
    )
      v-card.pa-5.my-4.list-item.col-3.col-xs-12.col-sm-6.col-md-4(
        v-for="(area, i) in excludeAreas" :key="i"
        outlined
      )
        v-text-field.pt-0(
          v-model="area.name"
          label="名称"
        )
        p.d-flex
          v-text-field.pt-0(
            v-model="area.height"
            label="高さ"
            suffix="cm"
            type="number"
            hide-details="false"
            :disabled="area.useRoomHeight"
          )
          v-checkbox.pt-0.pl-4(
            v-model="area.useRoomHeight"
            label="部屋の高さ"
            hide-details="false"
            small
          )
        v-text-field.pt-4(
          v-model="area.width"
          label="幅"
          suffix="cm"
          type="number"
          hide-details="false"
        )
        hr.mx-n5.mt-8.mb-5
        p.mb-0.d-flex.justify-space-between
          span 面積 {{ ( (area.useRoomHeight ? room.height : area.height ) * area.width ).toLocaleString() }}
          v-btn(@click="removeExcludeArea(i)") 削除
    p.text-center
      v-btn.my-2(@click="addExcludeArea") 追加
  v-card.pa-8.mt-4
    //- v-card-title.pa-0 除外後の面積
    h4 室内の面積
    p {{ (room.height * (room.widthY + room.widthX) * 2).toLocaleString() }} cm2
    h4 除外したい面積の合計
    p {{ rsExcludeAreaSize.toLocaleString() }} cm2
    h3 除外後の面積
    p {{ ( (room.height * (room.widthY + room.widthX) * 2) - rsExcludeAreaSize ).toLocaleString() }}
</template>

<script>
import PlaceData from '../components/v1/PlaceData.vue'
export default {
  data: () => ({
    room: {
      height: null,
      widthX: null,
      widthY: null,
    },
    excludeAreas: []
  }),
  methods: {
    addExcludeArea() {
      this.excludeAreas.push({
        name: '',
        height: null,
        width: null,
        useRoomHeight: false
      })
    },
    removeExcludeArea(i) {
      this.excludeAreas.splice(i, 1)
    }
  },
  computed: {
    rsExcludeAreaSize () {
      return this.excludeAreas.reduce((num, area) => {
        if ((area.height || (area.useRoomHeight && this.room.height)) && area.width) {
          if (area.useRoomHeight) num += this.room.height * area.width
          else num += area.height * area.width
        }
        return num
      }, 0) || 0
    }
  }
}
</script>

<style lang="sass" scoped>
.list-item
  transition: all 0.3s
  // display: inline-block
  // margin-right: 10px
.list-enter, .list-leave-to
  opacity: 0
  transform: translateY(30px)
.list-leave-active
  position: absolute
</style>
