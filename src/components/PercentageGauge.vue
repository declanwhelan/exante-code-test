<template>
  <div class="PercentageGauge">
    <svg
      xmlns="http://www.w3.org/2000/svg"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      viewbox="0 0"
    >
      <!--  arcs, one for grey, one for the indicator one  -->
      <path ref="arc" class="arc grey" d="M 50 180 A 60 60 0 0 1 270 180" style="fill: none" />
      <path class="arc gotten" d="M 50 180 A 60 60 0 0 1 270 180" style="fill: none" />
      <!--  tags for percentages around the arc  -->
      <text class="tag" x="50%" y="60" fill="black">{{ centerIndicator }}%</text>
      <text class="tag" x="20" y="170" fill="black">{{ leftIndicator }}%</text>
      <text class="tag" x="300" y="170" fill="black">{{ rightIndicator }}%</text>
      <!-- big text that shows the main percentage in the center  -->
      <text x="50%" y="170" class="middle" fill="black" text-anchor="middle">
        {{ userPercentage.toFixed() }}%
      </text>

      <!--  line which appears below the center tag at the crux of the arc  -->
      <line
        class="delimiter"
        x1="50%"
        y1="62"
        x2="50%"
        y2="80"
        stroke="black"
        stroke-dasharray="5,1"
      />
    </svg>
    <div class="PercentageGauge__graphName">Total Solar Irradiation</div>
    <div class="PercentageGauge__summary">
      Since {{ startDate }}, your panels have received {{ userPercentage.toFixed() }}% of the total
      expected sun.
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  props: {
    startDate: {
      type: String,
      required: true
    },
    actual: {
      type: Number,
      required: true
    },
    expected: {
      type: Number,
      required: true
    }
  },
  setup(props) {
    const arc = ref(null)

    const userPercentage = computed(() => {
      return (props.actual / props.expected) * 100
    })

    // needed to do the svg trickery with the dashes
    const arcLength = computed(() => {
      return arc.value?.getTotalLength().toFixed(2) ?? 0
    })

    // get the nearest value to percentage value minus 10. eg: for 102 we need 90
    const leftIndicator = computed(() => {
      const val = (Math.floor(userPercentage.value / 10) - 1) * 10
      return val >= 0 ? val : 0
    })
    const rightIndicator = computed(
      () => leftIndicator.value + (centerIndicator.value - leftIndicator.value) * 2
    )
    const centerIndicator = computed(() => {
      if (leftIndicator.value === 0) return 10
      return Math.floor(userPercentage.value / 10) * 10
    })

    /*
     * how much of the gauge is taken up. eg: if it goes from 60-140
     * and our user percentage value is 74, this will be 14
     */
    const gaugeValue = computed(() => userPercentage.value - leftIndicator.value)

    // how much of the arc will be taken up with color - uses gaugeValue
    const arcPercentage = computed(() => {
      const gaugeSize = rightIndicator.value - leftIndicator.value
      return (gaugeValue.value / gaugeSize) * 100
    })

    return {
      arc,
      arcLength,
      userPercentage,
      leftIndicator,
      rightIndicator,
      centerIndicator,
      gaugeValue,
      arcPercentage
    }
  }
}
</script>

<style lang="scss" scoped>
.PercentageGauge {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: white;

  svg {
    display: block;
    height: 200px;
    width: 325px;

    path {
      stroke-width: 12;
    }
    line.delimiter {
      stroke-width: 2;
    }
    text.tag {
      font-size: 16px;
      text-anchor: middle;
    }
    text.middle {
      font-size: 48px;
    }
    path.grey {
      stroke: lightgrey;
    }

    path.gotten {
      stroke: #4d88bf;
      stroke-dasharray: v-bind(arcLength) 9999;
      /* for 80% we'd do `full length - ((full length/100) * 80))`*/
      stroke-dashoffset: calc(
        v-bind(arcLength) - ((v-bind(arcLength) / 100) * v-bind(arcPercentage))
      );
      animation: circle_stroke 2s ease-in forwards;
    }
  }

  .PercentageGauge__graphName {
    font-weight: bold;
  }
  .PercentageGauge__summary {
    margin-top: 24px;
    text-align: center;
    max-width: 320px;
  }
}
@keyframes circle_stroke {
  0% {
    stroke-dashoffset: v-bind(arcLength);
  }
}
</style>
