<template>
  <div class="qualitynomination-popup">
    <a
      v-bind:href="suggestLink"
      v-on:click="onShowSuggestion"
      v-show="showSuggestLink"
    >
      <slot name="link-title">
        {{ T.qualityNominationRateProblem }}
      </slot>
    </a>
    <transition name="fade">
      <form
        class="panel panel-default popup"
        v-on:submit.prevent=""
        v-show="showForm"
      >
        <button class="close" type="button" v-on:click="onHide(true)">×</button>
        <div class="container-fluid">
          <template v-if="currentView === 'content'">
            <slot
              name="popup-content"
              v-bind:onSubmit="onSubmit"
              v-bind:sortedProblemTags="sortedProblemTags"
              v-bind:onHide="onHide"
            >
              <div class="title-text">
                {{
                  this.solved
                    ? T.qualityFormCongrats
                    : T.qualityFormRateBeforeAC
                }}
              </div>
              <div class="form-group">
                <label class="control-label">
                  {{ T.qualityFormDifficulty }}
                </label>
                <br />
                <label class="radio-inline"
                  ><input type="radio" v-model="difficulty" value="0" />
                  {{ T.qualityFormDifficultyVeryEasy }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="difficulty" value="1" />
                  {{ T.qualityFormDifficultyEasy }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="difficulty" value="2" />
                  {{ T.qualityFormDifficultyMedium }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="difficulty" value="3" />
                  {{ T.qualityFormDifficultyHard }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="difficulty" value="4" />
                  {{ T.qualityFormDifficultyVeryHard }}</label
                >
              </div>
              <div class="form-group">
                <label class="control-label">
                  {{ T.qualityFormTags }}
                  <ul class="tag-select">
                    <li
                      class="tag-select"
                      v-for="problemTopic in sortedProblemTags"
                    >
                      <label class="tag-select"
                        ><input
                          type="checkbox"
                          v-bind:value="problemTopic.value"
                          v-model="tags"
                        />
                        {{ problemTopic.text }}</label
                      >
                    </li>
                  </ul></label
                >
              </div>
              <div class="formGroup">
                <label class="control-label">{{ T.qualityFormQuality }}</label
                ><br />
                <label class="radio-inline"
                  ><input type="radio" v-model="quality" value="0" />
                  {{ T.qualityFormQualityVeryBad }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="quality" value="1" />
                  {{ T.qualityFormQualityBad }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="quality" value="2" />
                  {{ T.qualityFormQualityFair }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="quality" value="3" />
                  {{ T.qualityFormQualityGood }}</label
                >
                <label class="radio-inline"
                  ><input type="radio" v-model="quality" value="4" />
                  {{ T.qualityFormQualityVeryGood }}</label
                >
              </div>
              <div class="button-row">
                <div class="col-md-4"></div>
                <button
                  class="col-md-4 btn btn-primary"
                  type="submit"
                  v-bind:disabled="
                    !this.quality && !this.tags.length && !this.difficulty
                  "
                  v-on:click="onSubmit"
                >
                  {{ T.wordsSend }}
                </button>
                <button
                  class="col-md-4 btn btn-default"
                  type="button"
                  v-on:click="onHide(true)"
                >
                  {{ T.wordsCancel }}
                </button>
              </div>
            </slot>
          </template>
          <template v-if="currentView === 'thanks'">
            <div class="thanks-title">
              {{ T.qualityFormThanksForReview }}
            </div>
          </template>
        </div>
      </form>
    </transition>
  </div>
</template>

<style>
.qualitynomination-popup .popup {
  position: fixed;
  bottom: 10px;
  right: 4%;
  z-index: 9999999 !important;
  width: 550px;
  height: 443px;
  margin: 2em auto 0 auto;
  border: 2px solid #ccc;
  padding: 1em;
  overflow: auto;
}

.qualitynomination-popup .control-label {
  width: 100%;
}

.qualitynomination-popup .button-row {
  margin: 4px 0;
}

.qualitynomination-popup .fade-enter-active,
.qualitynomination-popup .fade-leave-active {
  transition: opacity 0.5s;
}

.qualitynomination-popup .fade-enter,
.qualitynomination-popup .fade-leave-to {
  opacity: 0;
}

.qualitynomination-popup .required .control-label:before {
  content: '*';
  color: red;
  position: absolute;
  margin-left: -10px;
}

.qualitynomination-popup .title-text {
  font-weight: bold;
  font-size: 20px;
  padding-bottom: 8px;
  text-align: center;
}

.qualitynomination-popup .tags-container {
  height: 148px;
}

.qualitynomination-popup .thanks-title {
  display: block;
  font-size: 2em;
  font-weight: bold;
  padding-left: 140px;
  padding-top: 148px;
}

ul.tag-select {
  height: 185px;
  overflow: auto;
  border: 1px solid #ccc;
}

ul.tag-select {
  list-style-type: none;
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

li.tag-select {
  margin: 0;
  padding: 0;
}

label.tag-select {
  font-weight: normal;
  display: block;
  color: WindowText;
  background-color: Window;
  margin: 0;
  padding: 0;
  width: 100%;
}

label.tag-select:hover {
  background-color: Highlight;
  color: HighlightText;
}
</style>

<script lang="ts">
import { Vue, Component, Prop, Watch } from 'vue-property-decorator';
import omegaup from '../../api.js';
import { T } from '../../omegaup.js';
import UI from '../../ui.js';

interface ProblemTag {
  text: string;
  value: string;
}

@Component
export default class QualityNominationPopup extends Vue {
  @Prop({ default: false }) solved!: boolean;
  @Prop({ default: true }) tried!: boolean;
  @Prop({ default: false }) nominated!: boolean;
  @Prop({ default: false }) nominatedBeforeAC!: boolean;
  @Prop({ default: false }) dismissed!: boolean;
  @Prop({ default: true }) dismissedBeforeAC!: boolean;
  @Prop({ default: true }) canNominateProblem!: boolean;
  @Prop({
    default: () => [
      'problemTopic2Sat',
      'problemTopicArrays',
      'problemTopicBacktracking',
      'problemTopicBigNumbers',
      'problemTopicBinarySearch',
      'problemTopicBitmasks',
      'problemTopicBreadthDepthFirstSearch',
      'problemTopicBruteForce',
      'problemTopicBuckets',
      'problemTopicCombinatorics',
      'problemTopicDataStructures',
      'problemTopicDisjointSets',
      'problemTopicDivideAndConquer',
      'problemTopicDynamicProgramming',
      'problemTopicFastFourierTransform',
      'problemTopicGameTheory',
      'problemTopicGeometry',
      'problemTopicGraphTheory',
      'problemTopicGreedy',
      'problemTopicHashing',
      'problemTopicIfElseSwitch',
      'problemTopicImplementation',
      'problemTopicInputOutput',
      'problemTopicLoops',
      'problemTopicMath',
      'problemTopicMatrices',
      'problemTopicMaxFlow',
      'problemTopicMeetInTheMiddle',
      'problemTopicNumberTheory',
      'problemTopicParsing',
      'problemTopicProbability',
      'problemTopicShortestPath',
      'problemTopicSimulation',
      'problemTopicSorting',
      'problemTopicStackQueue',
      'problemTopicStrings',
      'problemTopicSuffixArray',
      'problemTopicSuffixTree',
      'problemTopicTernarySearch',
      'problemTopicTrees',
      'problemTopicTwoPointers',
    ],
  })
  possibleTags!: string[];
  @Prop() problemAlias!: boolean;

  T = T;
  UI = UI;
  currentView = 'content';
  difficulty = '';
  quality = '';
  showFormOverride = true;
  localDismissed = this.dismissed || (this.dismissedBeforeAC && !this.solved);
  localNominated = this.nominated || (this.nominatedBeforeAC && !this.solved);
  tags: string[] = [];

  get showForm(): boolean {
    return (
      this.showFormOverride &&
      (this.solved || this.tried) &&
      !this.localNominated &&
      !this.localDismissed &&
      this.canNominateProblem
    );
  }

  get showSuggestLink(): boolean {
    return (this.tried || this.solved) && !this.localNominated;
  }

  get sortedProblemTags(): ProblemTag[] {
    return this.possibleTags
      .map(
        (x: string): ProblemTag => {
          return {
            value: x,
            text: this.T[x],
          };
        },
      )
      .sort((a: ProblemTag, b: ProblemTag): number => {
        return a.text.localeCompare(b.text, this.T.lang);
      });
  }

  get suggestLink(): string {
    let self = this;
    if (!self.problemAlias) {
      return '#';
    }
    return `#problems/${self.problemAlias}`;
  }

  onHide(isDismissed: boolean): void {
    this.showFormOverride = false;
    if (isDismissed) {
      this.$emit('dismiss', this);
    }
  }

  onShowSuggestion(): void {
    this.showFormOverride = true;
    this.localDismissed = false;
  }

  onSubmit(): void {
    this.$emit('submit', this);
    this.currentView = 'thanks';
    this.localNominated = true;

    setTimeout(() => this.onHide(false), 1000);
  }

  @Watch('dismissed')
  onDismissedChange(newValue: boolean, oldValue: boolean) {
    this.localDismissed = newValue;
  }

  @Watch('nominated')
  onNominatedChange(newValue: boolean, oldValue: boolean) {
    this.localNominated = newValue;
  }
}
</script>
