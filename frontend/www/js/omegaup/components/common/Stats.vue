<template>
  <div class="post">
    <div class="copy">
      <h1>{{ T.liveStatistics }}</h1>
      <div>
        {{ totalRuns }}
      </div>
      <highcharts v-bind:options="verdictChartOptions"></highcharts>
      <highcharts v-bind:options="distributionChartOptions"></highcharts>
      <highcharts v-bind:options="pendingChartOptions"></highcharts>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop, Watch } from 'vue-property-decorator';
import { T } from '../../omegaup.js';
import UI from '../../ui.js';
import omegaup from '../../api.js';
import { Chart } from 'highcharts-vue';

@Component({
  components: {
    highcharts: Chart,
  },
})
export default class Stats extends Vue {
  @Prop() stats!: omegaup.Stats;
  @Prop() verdictChartOptions!: Chart;
  @Prop() distributionChartOptions!: Chart;
  @Prop() pendingChartOptions!: Chart;

  T = T;

  get totalRuns(): string {
    return UI.formatString(T.totalRuns, { numRuns: this.stats.total_runs });
  }

  @Watch('stats')
  onPropertyChanged(newValue: omegaup.Stats): void {
    this.$emit('update-series', newValue);
  }
}
</script>
