<template>
  <div
    class="page"
  >
    <div v-show="isActivity" class="rain-wrap" @click="redPacketClick">
      <div
        class="r-content"
        :style="{
          top: `${rcTop}rem`,
          left: `${rcLeft}rem`,
          transition: `all ${activityTime}s linear`
        }"
      >
        <img
          v-for="packet in redPackets"
          :key="packet.id"
          src="../assets/packet.png"
          class="r-packet"
          :class="{ sPacket: packet.small }"
          :style="{ left: `${packet.x}` + 'rem', top: `${packet.y}` + 'rem' }"
        />
      </div>
    </div>
    <div v-show="!isActivity" class="s-wrap">
      <div class="s-bt" @click="countdownStart">点我开抢</div>
    </div>

    <div v-if="countdownFlag" class="countdown-wrap">
      <div class="c-t-num">3</div>
      <div class="c-t-num">2</div>
      <div class="c-t-num">1</div>
      <div class="c-t-num">GO</div>
    </div>
    <div
      class="red-hint"
      :class="{ 'r-ani': isClickedRedPacket, 'd-none': !isActivity }"
    >
      <div class="cheer-wrap" :class="{ hidden: !showHint }">
        <div class="c-text">{{ cheerData[hintIndex]?.text }}</div>
      </div>
      <div class="r-num" :class="{ hidden: !isShowNum }"
        >+{{ getRedPackets.length }}</div
      >
      <div class="r-card">
        <div class="r-c-t-1">连击</div>
        <div class="r-c-t-2">抢积分红包</div>
      </div>
    </div>

    <div class="popup" :class="{'hidden': !showPrize}">
      <div class="prize-wrap">
        <div class="prize-card">
          <div class="p-c-t-1">恭喜抢到</div>
          <div class="p-c-t-2"
            >{{ `${getRedPackets.length}` }}<text class="p-c-t-2_1">个红包</text></div
          >
        </div>
        <img
          class="alert-close"
          src="../assets/alert_close.png"
          mode="widthFix"
          @click="
            () => {
              // @ts-ignore
              this.showPrize = false
            }
          "
        />
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'Index',
  props: {
    // 红包雨滚动速率，数值越大下落速度越快
    speed: {
      type: Number,
      default: 6
    },
    // 红包雨持续时间，默认10s
    activityTime: {
      type: Number,
      default: 10
    },

  },
  data() {
    return {
      isClickedRedPacket: false, // 红包点击动画控制
      showPrize: false, // 奖励弹框
      showHint: false,
      hintIndex: 10,
      countdownFlag: false,
      isActivity: false,
      redPackets: [],
      getRedPackets: [],
      pId: 0,
      rcTop: 0,
      rcLeft: 0,
      cheerData: {
        10: {
          text: '连击666！'
        },
        20: {
          text: '点快点儿！'
        },
        30: {
          text: '太棒了！'
        },
        40: {
          text: '哇哦，优秀！'
        },
        50: {
          text: '加油，速度逆天了！'
        },
        60: {
          text: '太棒了！'
        },
        70: {
          text: '连击666！'
        },
        80: {
          text: '太棒了！'
        },
        90: {
          text: '连击666！'
        },
        100: {
          text: '太棒了！'
        }
      },
    }
  },
  computed: {
    isShowNum() {
      return this.getRedPackets.length > 0
    }
  },
  watch: {
    'getRedPackets.length'(val) {
      if (val > 0 && val < 101 && val % 10 === 0) {
        this.showHint = true
        this.hintIndex = val
      }
    }
  },
  methods: {
    redPacketClick() {
      if (!this.isActivity) {
        return
      }
      // 每次点击即添加一个新红包
      this.getRedPackets.push({})
      // 触发红包点击动画效果
      this.isClickedRedPacket = false
      setTimeout(() => {
        this.isClickedRedPacket = true
      })
    },
    // 倒计时开始
    async countdownStart() {
      // 开启倒计时
      this.countdownFlag = true
      setTimeout(() => {
        // 倒计时结束，开始红包雨
        this.countdownFlag = false
        this.start()
      }, 3200)
    },
    // 活动开始
    async start() {
      this.isActivity = true
      let sY = 0
      // 红包总行数
      const totalCount = this.activityTime * this.speed
      // 红包群稀疏集中的周期
      let period = 20
      for (let i = 0; i < totalCount; i++) {
        // 每个水平位置上放置两个红包
        let rowNum = 2
        period--
        if (period === 0) {
          if (rowNum === 2) {
            rowNum = 6
          } else {
            rowNum = 2
          }
          period = 10
        }
        for (let i = 0; i < rowNum; i++) {
          const section = 750 / rowNum
          let base = i * section
          this.redPackets.push({
            x: base + Math.random() * section,
            y: sY + Math.random() * 70, // 垂直距离上70的距离随机分布
            small: Math.random() > 0.5, // 随机生成小型红包
            id: this.pId++
          })
        }
        // 每行红包垂直间距200
        sY += 200
      }
      // 红包雨开始的位置
      this.rcTop = 0 - sY
      this.rcLeft = sY * 0.268 // sY * tan(15)
      setTimeout(() => {
        // 红包雨结束时的位置
        this.rcTop = 0
        this.rcLeft = 100
      }, 100)

      setTimeout(async () => {
        // 红包雨结束
        this.isActivity = false
        this.showPrize = true
        this.$emit('done')
      }, this.activityTime * 1000)
    }
  }
}
</script>

<style scoped lang="scss">
.page {
  width: 100%;
  min-height: 100vh;
  background-size: cover;
  background-image: url('https://i.328888.xyz/2023/01/06/k485X.png');

  .popup {
    width: 100%;
    height: 100vh;
    background: rgba(0,0,0,0.6);
    position: fixed;
    top: 0;
    left: 0;
    z-index: 99;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: all 0.3s;
  }

  .hidden {
    visibility: hidden;
    opacity: 0;
  }

  .r-bt {
    width: 174rem;
    height: 64rem;
    background: #fdf6d9;
    font-size: 28rem;
    font-weight: 500;
    color: #ee3320;
    line-height: 64rem;
    text-shadow: 0 1rem 0 #ffeeab;
    text-align: center;
    border-radius: 99px 0 0 99px;
    position: absolute;
    top: 258rem;
    right: 0;
  }

  .s-wrap {
    padding-top: 468rem;
  }

  .s-bt {
    width: 530rem;
    height: 100rem;
    line-height: 100rem;
    background: linear-gradient(180deg, #fdf6d9 0%, #eda863 100%);
    border-radius: 728rem;
    text-align: center;
    font-size: 40rem;
    font-weight: 500;
    color: #ee3320;
    position: absolute;
    bottom: 200rem;
    left: 50%;
    transform: translateX(-50%);
  }

  .rain-wrap {
    width: 100%;
    height: 100vh;
    position: relative;
    overflow: hidden;
    background: rgb(0 0 0 / 60%);
  }

  .r-content {
    position: relative;
    top: -400px;
    transform: rotate(15deg);
  }

  .r-packet {
    position: absolute;
    width: 112rem;
    height: 224rem;
  }

  .sPacket {
    width: 72rem;
    height: 144rem;
  }

  .countdown-wrap {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 100vh;
    background: rgb(0 0 0 / 60%);
    display: flex;
    justify-content: center;
    align-items: center;

    .c-t-num {
      position: absolute;
      font-size: 288rem;
      font-family: 'D-DIN Exp', sans-serif;
      font-weight: bold;
      line-height: 338rem;
      background: linear-gradient(180deg, #faf6e8 0%, #eda863 100%);
      background-clip: text;
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      color: transparent;
      transform: scale(0);
      animation: ani 0.8s;

      &:nth-child(2) {
        animation-delay: 0.8s;
      }

      &:nth-child(3) {
        animation-delay: 1.6s;
      }

      &:nth-child(4) {
        animation-delay: 2.4s;
      }
    }

    @keyframes ani {
      0% {
        transform: scale(0);
      }

      //50% {
      //  transform: scale(1);
      //}

      100% {
        transform: scale(1);
      }
    }
  }

  .r-ani {
    animation: r-ani 0.3s;
  }

  @keyframes r-ani {
    0% {
      transform: translate(-50%, -50%) scale(1);
    }

    50% {
      transform: translate(-50%, -50%) scale(1.1);
    }

    100% {
      transform: translate(-50%, -50%) scale(1);
    }
  }

  .red-hint {
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%) scale(1);
    text-align: center;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 70%;
    pointer-events: none;

    .cheer-wrap {
      display: flex;

      .c-text {
        font-size: 64rem;
        color: #fff;
        line-height: 75rem;
        height: 75rem;
        // -webkit-text-stroke: 2rem #fa4940;
        text-shadow: #fa4940 2rem 2rem 0;
        background-clip: text;
        white-space: nowrap;
      }
    }

    .r-num {
      height: 94rem;
      font-size: 80rem;
      color: #fff;
      line-height: 94rem;
      background-clip: text;
    }

    .r-card {
      width: 288rem;
      height: 388rem;
      background-image: url('../assets/rCard.png');
      background-size: 100% 100%;
      padding-top: 60rem;
      box-sizing: border-box;
      text-align: center;

      .r-c-t-1 {
        font-size: 34rem;
        color: #f1523f;
      }

      .r-c-t-2 {
        font-size: 28rem;
        color: #f04d3c;
      }

      .r-c-t-3 {
        margin-top: 126rem;
        font-size: 34rem;
        color: #fbecca;
      }
    }
  }

  .prize-wrap {
    display: flex;
    flex-direction: column;
    align-items: center;

    .prize-card {
      background-image: url('../assets/pCard.png');
      background-size: 100% 100%;
      padding-top: 110rem;
      width: 492rem;
      height: 660rem;
      text-align: center;
      box-sizing: border-box;
      display: flex;
      flex-direction: column;
      align-items: center;

      .p-c-t-1 {
        font-size: 32rem;
        color: #f04d3c;
        line-height: 38rem;
      }

      .p-c-t-2 {
        font-size: 88rem;
        font-family: 'D-DIN Exp', sans-serif;
        font-weight: bold;
        color: #f1523f;
        line-height: 103rem;
        margin-top: 16rem;
      }

      .p-c-t-2_1 {
        font-family: normal;
        font-weight: 400;
        font-size: 32rem;
        color: #f04d3c;
        line-height: 38rem;
      }
    }

    .alert-close {
      width: 64rem;
      margin-top: 32rem;
      width: 64rem;
    }
  }

  .d-none {
    display: none;
  }
}
</style>
