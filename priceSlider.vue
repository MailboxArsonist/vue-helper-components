<template lang="html">
  <div
    :class="[
      `range-${color}`,
      {'disabledx':disabled}
    ]"
    class="con-range"
    @mousewheel.prevent="mousewheelx"
    @keydown.left="keydownLeft"
    @keydown.right="keydownRight">
    <button
      ref="range"
      :disabled="disabled"
      class="range"
      @click="clickSlider($event),isSliderActive = true">
      <span
        :style="styleLineOne"
        :class="{'hasTransition':effect}"
        class="range-line-one" />
      <span class="range-line-mincircle" />

      <span
        :class="{'run-effect':effect}"
        :style="styleEfect"
        class="range-line-efect" />
    </button>
    <button
      ref="circle1"
      :disabled="disabled"
      :class="{
        'hasTransition':effect,
        'isEquals':isEquals,
        'changeValue':changeValue,
        'isEndValue':value == max
      }"
      :style="styleCircle"
      class="circle-range circles-range range--circles range--circle"
      @touchstart="activeFocus($event),isSliderActive = true"
      @mousedown="activeFocus($event),isSliderActive = true">
      <span
        :style="styleText"
        class="text-circle-range range--circle-text">
        {{ maxValueSelected }}
        <span v-if="textFixed">
          {{ textFixed }}
        </span>
      </span>
    </button>
    <button
      v-if="Array.isArray(value)"
      ref="circle2"
      :disabled="disabled"
      :class="{
        'hasTransition':effect,
        'isEquals':isEquals,
        'changeValue':changeValue,
        'isEndValue':value == max
      }"
      :style="styleCirclemincircle"
      class="circle-range-mincircle circles-range range--circles range--circle-mincircle"
      @touchstart="activeFocus($event),mincircle = true,isSliderActive = true"
      @mousedown="activeFocus($event),mincircle = true,isSliderActive = true">
      <span
        :style="styleText"
        class="text-circle-range-bottom range--circle-text-bottom">
        {{ minValueSelected }}
        <span v-if="textFixed">
          {{ textFixed }}
        </span>
      </span>
    </button>
  </div>
</template>

<script>
export default {
  name: "Range",
  props: {
    value: {
      default: () => [],
      type: [Object, Array]
    },
    disabled: {
      default: false,
      type: [Boolean, String]
    },
    color: {
      default: "primary",
      type: String
    },
    max: {
      default: 100,
      type: [Number, String]
    },
    min: {
      default: 0,
      type: Number
    },
    ticks: {
      default: false,
      type: Boolean
    },
    step: {
      default: 1,
      type: [Number, String]
    },
    textFixed: {
      default: null,
      type: String
    }
  },
  data: () => ({
    maxValueSelected: 0,
    minValueSelected: 0,
    effect: false,
    mincircle: false,
    isSliderActive: false,
    changeValue: false,
    valueCircle1: 0,
    valueCircle2: 0
  }),
  computed: {
    isEquals() {
      return Array.isArray(this.value) ? this.value[0] == this.value[1] : false
    },
    countTicks() {
      return this.max + 1
    },
    /*
     * styles component
     */
    styleSlider() {
      return {
        background: "blue"
      }
    },
    styleLineOne() {
      let widthx = this.minValueSelected - this.maxValueSelected
      let maxValueSelected = this.maxValueSelected
      if (this.maxValueSelected > this.minValueSelected) {
        widthx = this.maxValueSelected - this.minValueSelected
        maxValueSelected = this.minValueSelected
      }
      return {
        width: `${widthx}%`,
        left: `${maxValueSelected}%`,
        background: "#cecece"
      }
    },
    styleCircle() {
      return {
        left: `${this.maxValueSelected}%`,
        border: `2px solid #6CBCAE`
      }
    },
    testCircle(){
      return this.maxValueSelected
    },
    styleCirclemincircle() {
      return {
        left: `${this.minValueSelected}%`,
        border: `2px solid #6CBCAE`
      }
    },
    styleEfect() {
      return {
        left: `${this.maxValueSelected}%`,
        background: "#"
      }
    },
    styleText() {
      return {
        background: "#6CBCAE"
      }
    }
  },

  watch: {
    value() {
      this.updateCircleValues()
    }
  },
  mounted() {
    this.updateCircleValues()
  },
  methods: {
    updateCircleValues() {
      if (Array.isArray(this.value)) {
        this.maxValueSelected = Math.round(
          ((this.value[1] - this.min) / (this.max - this.min)) * 100
        )
        this.minValueSelected = Math.round(
          ((this.value[0] - this.min) / (this.max - this.min)) * 100
        )
      } else {
        this.maxValueSelected = Math.round(
          ((this.value - this.min) / (this.max - this.min)) * 100
        )
      }
    },
    styleTicks(index) {
      let lengthPerStep = 100 / ((this.max - this.min) / this.step)
      let steps = Math.round(index / lengthPerStep)
      return {
        left: steps * lengthPerStep + "%"
      }
    },
    activeFocus() {
      window.addEventListener("mousemove", this.mouseMovex)
      window.addEventListener("mouseup", this.removeEvents)
      window.addEventListener("touchmove", this.mouseMovex)
      window.addEventListener("touchend", this.removeEvents)
    },
    mouseMovex(evt) {
      let range = this.$refs.range
      let maxValueSelected
      /*
       * change position left circle and bar
       */
      if (evt.type == "touchmove") {
        maxValueSelected =
          event.targetTouches[0].clientX - range.getBoundingClientRect().left
      } else {
        maxValueSelected = evt.clientX - range.getBoundingClientRect().left
      }
      if (Math.sign(maxValueSelected) == -1) {
        maxValueSelected = 0
      } else if (maxValueSelected > range.clientWidth) {
        maxValueSelected = range.clientWidth
      }
      this.moveCircles(maxValueSelected)
    },
    removeEvents() {
      this.isSliderActive = false
      this.mincircle = false
      window.removeEventListener("mouseup", this.removeEvents)
      window.removeEventListener("mousemove", this.mouseMovex)
      window.removeEventListener("touchmove", this.mouseMovex)
      window.removeEventListener("touchend", this.removeEvents)
      this.emitValues()
    },
    clickSlider(evt) {
      let range = this.$refs.range
      let maxValueSelected = evt.clientX - range.getBoundingClientRect().left
      this.effect = true
      setTimeout(() => {
        this.effect = false
      }, 200)
      let obtenerPorcentaje = (maxValueSelected / range.clientWidth) * 100
      let porcentajex = Math.round(obtenerPorcentaje)
      if (Array.isArray(this.value)) {
        if (
          Math.abs(porcentajex - this.maxValueSelected) >
          Math.abs(porcentajex - this.minValueSelected)
        ) {
          this.mincircle = true
        } else {
          this.mincircle = false
        }
      }
      this.moveCircles(maxValueSelected)
    },
    moveCircles(maxValueSelected) {
      let range = this.$refs.range
      let obtenerPorcentaje = (maxValueSelected / range.clientWidth) * 100
      let porcentajex = Math.round(obtenerPorcentaje)
      const lengthPerStep = 100 / ((this.max - this.min) / this.step)
      const steps = Math.round(porcentajex / lengthPerStep)
      let val = steps * lengthPerStep * (this.max - this.min) * 0.01 + this.min
      val = Math.round(val)
      if (this.ticks) {
        if (val > this.max) {
          val = this.max
          this[this.mincircle ? "minValueSelected" : "maxValueSelected"] = 100
        } else {
          // Check to ensure circles cant pass each other
          if(this.mincircle && (steps * lengthPerStep > this.maxValueSelected)) this.minValueSelected = this.maxValueSelected
          else if(!this.mincircle && (steps * lengthPerStep < this.minValueSelected)) this.maxValueSelected = this.minValueSelected
          else this[this.mincircle ? "minValueSelected" : "maxValueSelected"] = Math.round(
            steps * lengthPerStep
          )
        }
      } else {
        // Check to ensure circles cant pass each other
        if(this.mincircle && (steps * lengthPerStep > this.maxValueSelected)) this.minValueSelected = this.maxValueSelected
        else if(!this.mincircle && (steps * lengthPerStep < this.minValueSelected)) this.maxValueSelected = this.minValueSelected
        else this[this.mincircle ? "minValueSelected" : "maxValueSelected"] = Math.round(
          steps * lengthPerStep
        )
      }
      // Check to see if slider events are still active(this will fire for click on slider, not for sliding events)
      if(!this.isSliderActive) {
        this.emitValues()
      }
    },
    emitValues(){
      this.$emit("input", [this.minValueSelected, this.maxValueSelected])
    }
  }
}
</script>