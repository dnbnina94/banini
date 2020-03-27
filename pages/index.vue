<template>
  <div class="page--home">
      <div class="row h-100">
        <div class="col-lg-10 col-xl-10 offset-lg-1 offset-xl-1 h-100">
          <div class="row-no-padding h-100 columns-wrapper">
            <div class="d-flex flex-column justify-content-between left-column">
              <img class="logo" src="~/assets/img/logo.png" />
              <div class="score-wrapper text-center d-flex align-items-center justify-content-between">
                <div class="score-label">SCORE</div>
                <div class="d-flex">
                  <div class="score-value" v-for="(number, index) in numbers" :key="index">
                    {{ number }}
                  </div>
                </div>
              </div>
            </div>
            <div class="right-column d-flex">
              <div class="w-100 d-flex flex-column">
                <div class="timer-wrapper position-relative">
                  <div ref="countdown" class="countdown"></div>
                </div>
                <board></board>
              </div>
            </div>
          </div>
        </div>
      </div>
  </div>
</template>

<script>

import Board from "@/components/Board";

export default {

  data: () => ({
  }),

  components: {
    Board
  },

  watch: {
    gameStarted(val) {
      if (val) {
        this.$refs.countdown.classList.add("countdown-start");
        
        let timer = setInterval(() => {
          let newCountdown = this.countdown - 1;
          this.$store.dispatch("app/SET_STATE", {
            countdown: newCountdown
          })
          if (this.countdown == 0) {
            clearInterval(timer);
          }
        }, 1000)
      } 
    }
  },

  computed: {
    score() {
      return this.$store.getters["app/getState"]("score");
    },

    gameStarted() {
      return this.$store.getters["app/getState"]("gameStarted");
    },

    countdown() {
      return this.$store.getters["app/getState"]("countdown");
    },

    numbers() {
      let numbers = []
      let sNumber = this.score.toString();

      for (let i = 0, len = sNumber.length; i < len; i += 1) {
          numbers.push(+sNumber.charAt(i));
      }

      return numbers;

    }
  }

}

</script>

<style lang="scss" scoped>
@import "~bootstrap/scss/functions";
@import "~bootstrap/scss/variables";
@import "~bootstrap/scss/mixins";
.page--home {
  width: 100%;
  height: 100%;
  background: #FFD539;

  padding: 1rem 0;

  @media (min-width: 768px) and (orientation: portrait) {
    padding: 3rem 2rem;
  }

  @media (min-width: 992px) and (orientation: landscape) {
    padding: 5rem 0;
  }

  @media (min-width: 992px) and (orientation: portrait) {
    padding: 10rem 0;
  }

  @media (min-width: 1200px) and (orientation: landscape) {
    padding: 10vh 0;
  }

  @media (min-width: 1200px) and (orientation: portrait) {
    padding: 5rem 0;
  }

  .score-wrapper {

    font-family: 'Cheri-Liney';
    background-color: rgba(#FFE76B, 0.45); 
    border-radius: 10px;

    padding: 2rem;

    @media (max-width: 768px) and (orientation: landscape) {
      padding: 1.5rem 1rem;
    }

    @media (max-width: 768px) and (orientation: portrait) {
      padding: 1rem;
    }

    @media screen and (orientation: portrait) {
      margin-top: 1rem;
    }

    @media screen and (orientation: landscape) {
      flex-direction: column;
    }

    .score-label {
      font-weight: bold;
      color: #A12200;
      font-size: 0.6rem;

      @media screen and (orientation: portrait) {
        padding-top: 0.5rem;
      }

      @media (max-width: 992px) and (orientation: landscape) {
        padding-bottom: 0.8rem;
      }

      @media (min-width: 992px) and (orientation: landscape) {
        padding-bottom: 1.6rem;
      }

      @include media-breakpoint-up(md) {
        font-size: 0.7rem;
      }

      @include media-breakpoint-up(lg) {
        font-size: 0.8rem;
      }
    }

    .score-value {
      width: 2.2rem;
      text-align: center;
      font-size: 1.8rem;
      font-weight: bold;
      color: white;
      -webkit-text-stroke: 0.17rem #EF101E;

      transform: translateZ(2);

      @include media-breakpoint-down(sm) {
        width: 1.2rem;
        font-size: 1rem;
        -webkit-text-stroke: 0.07rem #EF101E;
      }

      @media screen and (orientation: portrait) {
        padding-top: 0.5rem;
      }
    }
  }

  .logo {
    width: 100%;
    margin: 0 auto;

    @media screen and (orientation: portrait) {
      width: 5.5rem;
    }

    @media (min-width: 768px) and (orientation:portrait) {
      width: 25%;
    }

  }

  .timer-wrapper {
    width: 100%;
    height: 1.8rem;
    background-image: url(~assets/img/pretzel-stick-bg2.png);
    background-size: 100% 100%;
    background-repeat: no-repeat;
    margin-bottom: 0.3rem;

    @include media-breakpoint-down(md) {
      height: 1.2rem;
      background-image: url(~assets/img/pretzel-stick-bg1.png);
    }

    @media screen and (orientation: portrait) {
      margin-top: 0.35rem;
    } 

    .countdown {
      position: absolute;
      width: 0;
      height: 100%;
      top: 0;
      right: 0;
      transition: width 60s linear;
      background: #FFD539;

      &-start {
        width: 100%;
      }
    }
  }

  .columns-wrapper {
    display: flex;
    height: 100%;

    @media screen and (orientation:portrait) {
        flex-direction: column;
    }

    .left-column {

      @media screen and (orientation:landscape) {
        width: 25%;
        padding-right: 0.5rem !important;
      }

      @media (min-width: 992px) and (orientation:landscape) {
        padding-right: 0.8rem !important;
      }
    }

    .right-column {
      flex-grow: 1;

      @media screen and (orientation:landscape) {
        width: 75%;
        height: 100%;
        padding-left: 0.5rem !important;
      }

      @media (min-width: 992px) and (orientation:landscape) {
        padding-left: 0.8rem !important;
      }
    }
  }
}
</style>
