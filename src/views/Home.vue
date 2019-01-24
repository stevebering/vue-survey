<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div v-bind:style="{'text-align': 'left'}">
      <div v-if="survey">
        <survey :survey="survey"/>
      </div>
      <div v-if="surveyResult">{{ surveyResult }}</div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import * as SurveyVue from "survey-vue";
let Survey = SurveyVue.Survey;
SurveyVue.StylesManager.applyTheme("default");

var surveyJSON = {
  title: "Tell us, what technologies do you use?",
  pages: [
    {
      name: "page1",
      questions: [
        {
          type: "radiogroup",
          choices: [
            {
              value: "yes",
              text: "Yes"
            },
            {
              value: "no",
              text: "No"
            }
          ],
          isRequired: true,
          name: "cssFrameworkUsing",
          title: "Do you use any css framework like Bootstrap?"
        },
        {
          type: "checkbox",
          choices: ["Bootstrap", "Foundation", "Skeleton"],
          hasOther: true,
          isRequired: true,
          defaultValue: ["Bootstrap"],
          name: "framework",
          title: "What front-end framework do you use?",
          visibleIf: "{cssFrameworkUsing} = 'yes'"
        },
        {
          type: "checkbox",
          choices: ["SASS", "LESS"],
          hasOther: true,
          isRequired: true,
          name: "compiler",
          title: "What CSS compiler do you use?",
          visibleIf: "{cssFrameworkUsing} = 'no'"
        }
      ]
    },
    {
      name: "page2",
      questions: [
        {
          type: "radiogroup",
          choices: ["Yes", "No"],
          isRequired: true,
          name: "mvvmUsing",
          title: "Do you use any MVVM framework?"
        },
        {
          type: "checkbox",
          choices: ["AngularJS", "KnockoutJS", "React", "Vue"],
          hasOther: true,
          isRequired: true,
          name: "mvvm",
          title: "What MVVM framework do you use?",
          visibleIf: "{mvvmUsing} = 'Yes'"
        }
      ]
    },
    {
      name: "page3",
      questions: [
        {
          type: "comment",
          name: "about",
          title:
            "Please tell us about your main requirements for Survey library"
        }
      ]
    }
  ]
};

@Component({
  components: {
    Survey
  }
})
export default class Home extends Vue {
  private survey?: any = null;
  private surveyResult?: any = null;
  mounted() {
    let model = new SurveyVue.Model(surveyJSON);
    model.showCompletedPage = false;
    model.clearInvisibleValues = "onHidden";
    model.onComplete.add(this.onSurveyComplete);
    this.survey = model;
  }

  private onSurveyComplete(result: any, options: any) {
    options.showDataSaving("Doing some saving now");
    this.surveyResult = result.data;
    setTimeout(() => options.showDataSavingSuccess("We did it!"), 2000);
  }
}
</script>
