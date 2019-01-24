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
// let defaultThemeColors = SurveyVue.StylesManager.ThemeColors["default"];
// defaultThemeColors["$main-color"] = "#7ff07f";
// defaultThemeColors["$main-hover-color"] = "#6fe06f";
// defaultThemeColors["$text-color"] = "#4a4a4a";
// defaultThemeColors["$header-color"] = "#7ff07f";
// defaultThemeColors["$header-background-color"] = "#4a4a4a";
// defaultThemeColors["$body-container-background-color"] = "#f8f8f8";

// SurveyVue.StylesManager.applyTheme("default");

// SurveyVue.defaultBootstrapCss.navigationButton = "btn btn-primary";
SurveyVue.StylesManager.applyTheme("bootstrap");

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
          choices: [
            { value: "bootstrap", text: "Bootstrap" },
            { value: "foundation", text: "Foundation" },
            { value: "skeleton", text: "Skeleton" }
          ],
          hasOther: true,
          isRequired: true,
          defaultValue: ["bootstrap"],
          name: "framework",
          title: "What front-end framework do you use?",
          visibleIf: "{cssFrameworkUsing} = 'yes'"
        },
        {
          type: "checkbox",
          choices: [
            { value: "sass", text: "SASS" },
            { value: "less", text: "LESS" }
          ],
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
    model.onValueChanged.add((survey, options) => {
      console.log("survey", survey);
      console.log("options", options);
      survey.title = `page ${survey.currentPageNo + 1} or ${survey.visiblePageCount}`;
    });
    this.survey = model;
  }

  private onSurveyComplete(result: any, options: any) {
    options.showDataSaving("Doing some saving now");
    this.surveyResult = result.data;
    setTimeout(() => options.showDataSavingSuccess("We did it!"), 2000);
  }
}
</script>
