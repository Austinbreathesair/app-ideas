<template>
  <div class="generator">
    <section class="hero is-primary is-bold has-text-centered py-6">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            What skills would you like to work on?
          </h1>
          <div v-for="skill in skillList"
               :key="skill.id">
            <div class="field">
              <label class="checkbox">
                <input type="checkbox"
                       v-model="selectedSkills"
                       :value="skill.id"
                       @change="generateFilteredAppList">
                {{ skill.skill}}
              </label>
            </div>
          </div>
        </div>
      </div>
    </section>

    <div class="container">
      <div class="column is multiline mt-3">
        <div v-for="app in filteredAppList"
             :key="app.id"
             class="column is-one-third">
          <div class="card">
            <header class="card-header">
              <p class="card-header-title is-uppercase is-size-5">
                {{app.app}}
              </p>
            </header>
            <div class="card-content">
              <div class="content has-text-left mb-4">
                <p class="is-size-7">{{ app.instructions }}</p>
                <h4>Skills:</h4>
                <ul v-for="skill in app.skills"
                    :key="skill.id">
                  <li>
                    <strong>{{ skillList[skill-1].skill }}</strong>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'Generator',
  setup() {
    const GENERATOR_BASE = 'http://localhost:3000';
    const skillList = ref([]);
    const selectedSkills = ref([]);
    const filteredAppList = ref([]);
    let appList = [];

    function generateFilteredAppList() {
      filteredAppList.value = [];

      for (const app of appList) {
        const appSkillsArray = app.skills;
        const selectedSkillsArray = selectedSkills.value;

        if (hasAllSkills(appSkillsArray, selectedSkillsArray)) {
          filteredAppList.value.push(app);
        }
      }
    }

    function hasAllSkills(appSkills, selectedSkills) {
      return selectedSkills.every((f) => appSkills.includes(f));
    }

    async function getSkillList() {
      const response = await fetch(`${GENERATOR_BASE}/skills`);
      skillList.value = await response.json();
    }

    async function getAppList() {
      const response = await fetch(`${GENERATOR_BASE}/apps`);
      appList = await response.json();
      filteredAppList.value = appList;
    }

    getSkillList();
    getAppList();

    return {
      skillList,
      selectedSkills,
      filteredAppList,
      generateFilteredAppList,
    };
  },
};
</script>

<style scoped>
  label {
    font-size: 20px;
  }
  .card {
    height: 100%;
  }
</style>