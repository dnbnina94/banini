<template>
  <div ref="board" class="board w-100 position-relative d-flex flex-center">
    <div 
      class="falling-object" 
      v-for="(obj, index) in objects" 
      :key="index"
      :style="obj.style"
      @mousedown="obj.special && !obj.clicked && specialClicked($event, obj)"
      :class="[obj.class, obj.fall]"
    ></div>
    <clap
      class="clap"
      :class="{'clap-clicked' : emojiClicked}"
      @mousedown="press"
      @mouseup="release"
    ></clap>
  </div>
</template>

<script>
import Clap from "@/assets/svg/clap.svg";

export default {
  name: "board",

  data: () => ({
    emojiClicked: false,
    objects: [],
    objectProperties: {
      sizes: [
        2.5,
        4,
      ],
      backgrounds: [
        "pretzel",
        "pretzel-stick",
        "cracker"
      ],
      specials: [
        {bg: "1", points: 50},
        {bg: "2", points: 100}
      ]
    }
  }),

  created() {},

  mounted() {},

  methods: {
    random(min, max) {
      return Math.round(Math.random() * (max - min) + min);
    },

    release() {
      setTimeout(() => {
        this.emojiClicked = false;
      }, 100);
    },

    press() {
      if (this.countdown == 0) {
        return;
      }

      // this.emojiClicked = true;
      this.$store.dispatch("app/SET_STATE", {
        score: this.score + 1,
        gameStarted: true
      });

      let newObjStyle = {};
      let obj = {};

      let special = Math.random() < 0.05;

      let size;

      if (!special) {
        size = this.sizes[this.random(0, this.sizes.length-1)];
        obj.class = "falling-object-" + this.backgrounds[this.random(0, this.backgrounds.length-1)];
      } else {
        size = 6;

        let randomSpecial = this.specials[this.random(0,this.specials.length-1)];
        obj.class = "falling-object-special falling-object-special-" + randomSpecial.bg;
        obj.special = true;
        obj.points = randomSpecial.points;
      }

      let device = window.innerWidth >= 992 ? "desktop" : window.innerWidth >= 768 ? "tablet" : "mobile";
      let viewWidth;
      let k;
      switch(device) {
        case "desktop":
          viewWidth = window.innerWidth/100;
          k = 1;
          break;
        case "tablet":
          viewWidth = 18;
          k = 0.85;
          break;
        case "mobile":
          viewWidth = 16;
          k = 0.7;
          break;
      }

      newObjStyle.width = k*size + "rem";
      newObjStyle.height = k*size + "rem";
      newObjStyle.left = this.random(0,this.$refs.board.clientWidth/viewWidth - k*size) + "rem";
      newObjStyle.top = "-" + k*size*1.5 + "rem";

      var velocity = this.random(850, 10000);
      newObjStyle.transition = "z-index 0.4s, opacity 0.4s, transform " + velocity + "ms linear";
      newObjStyle.transform = "rotate(" + this.random(0,360) + "deg)";

      obj.style = newObjStyle;

      this.objects.push(obj);

      this.$nextTick(() => {
        obj.fall = "move";
      });

      setTimeout(() => {
        this.emojiClicked = true;
      },50)

    },

    specialClicked(event, obj) {
      if (this.countdown == 0) {
        return;
      }

      obj.clicked = true;

      this.$store.dispatch("app/SET_STATE", {
        score: this.score + obj.points
      });

      this.$nextTick(() => {
        event.target.classList.add("clicked");
      })
    }
  },

  computed: {
    score() {
      return this.$store.getters["app/getState"]("score");
    },

    backgrounds() {
      return this.objectProperties.backgrounds;
    },

    specials() {
      return this.objectProperties.specials;
    },

    sizes() {
      return this.objectProperties.sizes;
    },

    countdown() {
      return this.$store.getters["app/getState"]("countdown");
    }
  },

  components: {
    Clap
  }
};
</script>

<style lang="scss" scoped>
@import "~bootstrap/scss/functions";
@import "~bootstrap/scss/variables";
@import "~bootstrap/scss/mixins";
.board {
  border-radius: 10px;
  // border: 3px solid black;
  background: #FFE76B;
  overflow: hidden;

  flex-grow: 1;
  height: 100% !important;
}

.clap {
  width: 5rem;
  z-index: 3;
  cursor: pointer;
  transition: transform 0.1s;

  @include media-breakpoint-down(sm) {
    width: 3.5rem;
  }

  &-clicked {
    transform: scale(0.8);

    @include media-breakpoint-down(sm) {
      transform: scale(0.8);
    } 
  }
}

.falling-object {
  position: absolute;
  background-repeat: no-repeat;
  background-size: cover;

  &-pretzel {
    background-image: url(~assets/img/pretzel.png);
  }

  &-cracker {
    background-image: url(~assets/img/cracker2.png);
  }

  &-pretzel-stick {
    background-image: url(~assets/img/pretzel-stick1.png);
    background-size: contain;
  }

  &-special {
    z-index: 2;
    cursor: pointer;

    &-1 {
      background-image: url(~assets/img/trik-special1.png);
    }

    &-2 {
      background-image: url(~assets/img/trik-special2.png);
    }
  }
}

.clicked {
  opacity: 0;
  z-index: 1;
  cursor: default;
}

.move {
  transform: translateY(120vh) !important;
}
</style>
