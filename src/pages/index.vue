<template>
  <v-container class = "bg-variant"> <!-- d-flex flex-column fill-height justify-center align-center " > -->
    <v-app-bar :elevation="0">
      <v-app-bar-title>
      <!--
        <p class="font-weight-bold text-sm-h2 text-h4 mt-2'"> CoPPE</p> <span class="font-weight-medium text-primary">Competitive Programing Problem Enumerator</span> --> CoPPE </v-app-bar-title>
      <template v-slot:append>

        <template v-if = "eMode">
          <v-btn icon @click="dialog = true"> <v-icon>mdi-plus</v-icon> </v-btn>
          <v-btn icon @click="dl()"> <v-icon>mdi-export</v-icon> </v-btn>
        </template>

        <v-btn icon @click="eMode = !eMode">

        <template v-if = "eMode">
          <v-icon>mdi-wrench</v-icon>
        </template>
        <template v-else>
          <v-icon>mdi-cog</v-icon>
        </template>
      </v-btn>

</template>
    </v-app-bar>

    <!-- -->
      <v-card flat>
        <template v-slot:text>
          <v-text-field
              v-model="search"
              label="Search"
              prepend-inner-icon="mdi-magnify"
              variant="outlined"
              hide-details
              single-line
          ></v-text-field>
        </template>

        <v-data-table :expand = "true" :headers="headers" :items="problems" :search="search">
          <template #item.local= "{item}">
            <a target="_blank" :href="item.link"> {{ item.local }} </a>
          </template>

          <template #item.tags="{ item }" >
            <v-sheet class="py-2">
                <v-responsive class="overflow-y-auto , ga-2" max-width = 350>
                  <v-row no-gutters class="ga-2">
                  <template v-for="i in item.tags" :key="i">
                    <v-chip
                        :color ="getColorTag(i)"
                        :text="i"
                        ></v-chip>
                  </template>
                  </v-row>
                </v-responsive>
            </v-sheet>
          </template>

          <template #item.dificulty = "{ item }">
            <v-chip label :color = "getColorDif(item.dificulty)" >
              {{ item.dificulty }}
            </v-chip>
          </template>


          <template v-if="eMode" #item.actions ="{item}">
            <v-icon
                size="small"
                @click="dialog = true, getItem(item)"
                >
                mdi-cog
            </v-icon>
          </template>

        </v-data-table>
      </v-card>


      <!-- MODAL -->
    <v-dialog v-model="dialog" max-width = 800>
      <v-card
          prepend-icon="mdi-update"
          title="Problem"
          >
          <v-card-text>

            <v-row dense>
              <v-col cols="3">
                <v-text-field
                    v-model="objDialog.code"
                    label="Code"
                    required
                    >
                </v-text-field>
              </v-col>
              <v-col cols="3">
                <v-select
                    v-model = "objDialog.local"
                    single-line
                    :items="origins"
                    label="Origin"
                    required
                  ></v-select>
              </v-col>
              <v-col cols="6">
                <v-text-field
                  v-model="objDialog.name"
                  label="Name"
                  required
                  >
                </v-text-field>
              </v-col>
            </v-row>

            <v-row dense >
              <v-col cols="12">
                <v-text-field
                    v-model = "objDialog.link"
                    label="Link"
                    required></v-text-field>
              </v-col>
            </v-row>

            <v-row dense>
              <v-col cols = "12" class = "align-center pa-4 ">
                <v-slider
                    v-model="objDialog.dificulty"
                    tick-size="5"
                    :step="100"
                    :max="3000"
                    :min="800"
                    hide-details
                    :color = "getColorDif(objDialog.dificulty)"
                    >
                    <template v-slot:append>
                      <v-number-input
                          v-model="objDialog.dificulty"
                          density="compact"
                          controlVariant="stacked"
                          :max="3000"
                          :min="800"
                          :step="100"
                          variant="outlined"
                          style="width: 120px"
                          type="number"
                          hide-details
                          single-line
                          ></v-number-input>
                    </template>
                </v-slider>
                </v-col >
            </v-row>
            <v-row dense>
              <v-divider></v-divider>
            </v-row>
            <v-row>
              <v-col cols = "12">
                <v-responsive class="overflow-y-auto">
                  <v-chip-group
                      v-model="objDialog.tags"
                      multiple
                      column
                      class = "overflow-y-auto"
                      >
                      <v-chip
                          v-for="tag in sepTags"
                          :key="tag"
                          :text="tag"
                          :value = "tag"
                          :color="getColorTag(tag)"
                          ></v-chip>
                  </v-chip-group>
                </v-responsive>
              </v-col>
            </v-row>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>

            <v-btn
                text="Cancel"
                variant="tonal"
                @click="dialog = false"
                ></v-btn>

            <v-btn
                text="Save"
                variant="tonal"
                @click="dialog = false, saveDialog()"
                ></v-btn>
          </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>


</template>
<script setup lang="ts">
  import { VNumberInput } from 'vuetify/labs/VNumberInput';
  import { problemsList } from "../problems.js";
  import { ref } from 'vue';

  const problems = ref([...problemsList]);

  const dialog = ref(false);
  const eMode = ref(false);
  const origins = ref(["Codeforces", "Spoj", "Uva", "Beecrowd", "AtCoder", "Vjudge"]);
  const indexDialog = ref(-1);

  const objDialog = ref({
      dificulty: 800,
      code: "",
      local: [],
      link: "",
      name: "",
      tags: []
  });

  let defaultDialog = {
      dificulty: 800,
      code: "",
      local: [],
      link: "",
      name: "",
      tags: []
  };


  const search = ref ('');
  const headers = ref([
      { align: 'start', key: 'code', title: 'Code'},
      { key: 'name', title: 'Name'},
      { key: 'local', title: 'From' },
      { key: 'tags', title: 'Tags' },
      { key: 'dificulty', title: 'Dificulty' },
      { key: 'actions', sortable: false }
  ]);


  // colors
  const tags = new Map();
  tags.set('Dynamic Programming', 'deep-orange');
  tags.set('Brute Force', 'deep-orange');
  tags.set('Graphs', 'purple-accent-1');
  tags.set('Data Structures', 'purple-accent-1');
  tags.set('Number Theory', 'cyan-accent-1');
  tags.set('Math', 'yellow');
  tags.set('MEX', 'yellow-accent-2');
  tags.set('Permutation', 'yellow-accent-2');
  tags.set('Matrices', 'yellow-accent-2');
  tags.set('Geometry', 'amber');
  tags.set('Binary Search', 'blue-accent-3');
  tags.set('Bitmasks', 'light-blue');
  tags.set('Strings', 'indigo-accent-1');
  tags.set('Greedy', 'green-accent-4');
  tags.set('Two Pointers', 'lime-accent-3');
  tags.set('Sorting', 'light-green-accent-4');

  //tags
  let sepTags = [...tags.keys()];

  function saveDialog(){
      if(indexDialog.value == -1) problems.value.push(objDialog.value);
      else Object.assign(problems.value[indexDialog.value], objDialog.value);
      resetDialog();
  }

  function resetDialog(){
      objDialog.value = defaultDialog;
  }

  function getItem(item){
      indexDialog.value = problems.value.indexOf(item);
      Object.assign(objDialog.value, problems.value[indexDialog.value]);
  }

  function getColorDif(rating) {
      if (rating < 1200) return 'gray';
      else if (rating < 1400) return 'green';
      else if (rating < 1600) return 'cyan';
      else if (rating < 1900) return 'blue';
      else if (rating < 2400) return 'yellow';
      else return 'red';
  }

  function getColorTag(tag) {
      return tags.get(tag);
  }

  function dl(){
      const filename = 'problemsList.js';
      const jsonStr = JSON.stringify(problems.value);
      let element = document.createElement('a');
      element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(jsonStr));
      element.setAttribute('download', filename);
      element.style.display = 'none';
      document.body.appendChild(element);
      element.click();
      document.body.removeChild(element);
  }
</script>
