<script>
import { ref } from 'vue'

export default {
  name: 'Generator',
  setup () {
    const GENERATOR_BASE = 'http://localhost:3000'
    const skillList = ref([])
    const selectedSkills = ref([])

    const filteredAppList = ref([])
    let appList = []

    async function getSkillList() {
      const response = await fetch(`${GENERATOR_BASE}/skills`)
      skillList.value = await response.json()
    }

    async function getAppList() {
      const response = await fetch(`${GENERATOR_BASE}/apps`)
      appList = await response.json()

      filteredAppList.value = appList
    }

    function generateFilteredAppList() {
      filteredAppList.value = []

      for(const app of appList) {
        const appSkillsArray = app.skills
        const selectedSkillsArray = selectedSkills.value

        if(hasAllSkills(appSkillsArray, selectedSkillsArray)) {
          filteredAppList.value.push(app)
        }
      }
    }

    function hasAllSkills(appSkills, selectedSkills) {
      return selectedSkills.every(f => appSkills.includes(f))
    }

    function getRandom(value) {
      let keys = Object.keys(value)
      return value[keys[keys.length * Math.random() << 0]]
    } 

    getAppList()
    getSkillList();

    return { skillList, selectedSkills, filteredAppList, generateFilteredAppList, getRandom }
  }
}
</script>

<template lang="pug">
div(class="generator")
  section(class="hero is-primary is-bold has-text-centered py-6")
    div(class="hero-body")
      div(class="container")
        h1(class="title") 
          | Em quais habilidades você quer trabalhar? 
        div(
          v-for="(skill, index) in skillList" 
          :key="index"
        )
          div(class="field")
            label(class="checkbox")
              input(
                type="checkbox" 
                :value="skill.id"
                v-model="selectedSkills"
                @change="generateFilteredAppList"
              )
              | {{ skill.skill }}
  div(class="container")
    div(class="columns is-multiline mt-3")
      div(v-for="(app, index) in filteredAppList" :key="index")
        div(class="card")
          header(class="card-header")
            p(class="card-header-title is-uppercase is-size-5")
              | {{ app.app }}
          div(class="card-content")
            div(class="content has-text-left mb-4")
              p(class="is-size-7")
                | {{ app.instructions }}
              h4 Skills: 
              ul(v-for="(skill, index) in app.skills" :key="index")
                li
                  strong {{ skillList[skill-1].skill }}
                  p(
                    v-if="skillList[skill-1].options" 
                    :set="randSkill = getRandom(skillList[skill-1].options) "
                  )
                    a(:href="randSkill") {{ randSkill }}
</template>

<style scoped>
label {
  font-size: 20px;
}

.card {
    height: 100%;
}
</style>