<template>
  <n-form inline label-placement="left" :show-feedback="false">
    <n-form-item label="Période">
      <n-date-picker
        v-model:value="$store.state.analysis.period"
        style="padding-right: 220px"
        type="datetimerange"
        :show-feedback="true"
        :update-value-on-close="true"
        @update:value="fetchSensors()"
      />
    </n-form-item>

    <n-space class="align-right">
      <n-select
        style="width: 200px"
        :value="getMeasurementId"
        :options="getMeasurements"
        :virtual-scroll="false"
        :show-feedback="true"
        filterable
        placeholder="Mesure affichée"
        @update:value="selectMeasurement"
      />
    </n-space>
  </n-form>
</template>
<script lang="ts">
  import { mapGetters, mapActions } from 'vuex'
  export default {
    name: 'Interval',
    computed: {
      ...mapGetters('analysis', [
        'getMeasurements',
        'getMeasurement',
        'getMeasurementId',
      ]),
    },
    methods: {
      ...mapActions('analysis', ['selectSensorByMesureId', 'fetchSensors']),
      selectMeasurement(value: any): void {
        this.selectSensorByMesureId(value)
      },
    },
  }
</script>
