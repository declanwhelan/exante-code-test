<template>
  <div class="IrradiationProgressCard">
    <h3>Your Solar Irradiation</h3>

    <div>
      <label for="expectedAmount"> Total expected solar energy this period: </label>
      <p class="IrradiationProgressCard__expectedAmount" name="expectedAmount">
        {{ accumulatedExpectedValues.toLocaleString('en-US') }}KWh
      </p>
    </div>

    <PercentageGauge
      :actual="accumulatedActualValues"
      :expected="accumulatedExpectedValues"
      :startDate="startDate"
    />
  </div>
</template>

<script>
import moment from 'moment'
import { computed } from 'vue'

import PercentageGauge from 'components/PercentageGauge.vue'
import IrradiationData from 'assets/datasets/irradiation.json'
import PoliciesData from 'assets/datasets/policies.json'

export default {
  components: {
    PercentageGauge
  },
  setup() {
    const actualValues = computed(() => IrradiationData.parameters[0].actualValues)
    const expectedValues = computed(() => IrradiationData.parameters[0].expectedValues)

    /*
     * current period is either now minus 6 months or
     * between the start contract and now, whichever
     * is more current
     */
    const currentPeriod = computed(() => {
      const sixMonthsAgo = moment().subtract(6, 'months')
      const policyStartDate = moment(
        PoliciesData.policies[0].policyStartDate,
        'YYYY-MM-DDTHH:mm:ss'
      )

      return {
        start: sixMonthsAgo.isBefore(policyStartDate) ? policyStartDate : sixMonthsAgo,
        end: moment().startOf('day')
      }
    })

    const startDate = computed(() => currentPeriod.value.start.format(' Do MMMM YYYY'))

    function getOverallBetweenDates(values, start, end) {
      let overall = 0
      values.forEach((expected) => {
        const elementDateTime = moment(expected.datetime)
        const isInPeriod = elementDateTime.isAfter(start) && elementDateTime.isBefore(end)
        if (isInPeriod) overall += Number(expected.value)
      })
      return overall
    }

    const accumulatedExpectedValues = computed(() => {
      const previousMonth = moment(currentPeriod.value.start).subtract(1, 'M')
      return getOverallBetweenDates(expectedValues.value, previousMonth, currentPeriod.value.end)
    })

    const accumulatedActualValues = computed(() => {
      return getOverallBetweenDates(
        actualValues.value,
        currentPeriod.value.start,
        currentPeriod.value.end
      )
    })
    return {
      startDate,
      accumulatedActualValues,
      accumulatedExpectedValues
    }
  }
}
</script>

<style lang="scss" scoped>
.IrradiationProgressCard {
  display: flex;
  flex-direction: column;
  position: relative;
  max-width: 510px;
  width: calc(100% - 40px);
  padding: 20px;
  background-color: white;
  box-shadow: 0 0 5px lightgray;
  border-radius: 6px;

  .IrradiationProgressCard__expectedAmount {
    font-weight: bold;
    margin-top: 8px;
  }
}
</style>
