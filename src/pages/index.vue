<template>
  <v-container class = "bg-variant"> <!-- d-flex flex-column fill-height justify-center align-center " > -->
    <v-app-bar :elevation="0">
      <v-app-bar-title>
      <!--
          <p class="font-weight-bold text-sm-h2 text-h4 mt-2'"> CoPPE</p> <span class="font-weight-medium text-primary">Competitive Programing Problem Enumerator</span> --> CoPPE </v-app-bar-title>
      <template v-slot:append>
        <v-btn icon @click="dialog = true"> <v-icon>mdi-plus</v-icon> </v-btn>
        <v-btn icon @click="dl()"> <v-icon>mdi-export</v-icon> </v-btn>
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

        <v-data-table :headers="headers" :items="problems" :search="search">
          <template #item.local= "{item}">
            <a target="_blank" :href="item.link"> {{ item.local }} </a>
          </template>

          <template #item.tags="{ item }" >
            <v-row>
              <v-cols cols= "3" >
                  <template v-for="i in item.tags" :key="i">
                    <v-chip :color ="getColorTag(i)"> {{ i }} </v-chip>
                  </template>

                  <div class="d-flex justify-center ga-2 overflow-y " max-width = '200'>
                  </div>
              </v-cols>
            </v-row>
          </template>

          <template #item.dificulty = "{ item }">
            <v-chip label :color = "getColorDif(item.dificulty)" >
              {{ item.dificulty }}
            </v-chip>
          </template>

        </v-data-table>
      </v-card>


      <!-- MODAL -->
    <v-dialog v-model="dialog" width="auto">
      <v-card
          max-width="400"
          prepend-icon="mdi-update"
          text="Your application will relaunch automatically after the update is complete."
          title="Update in progress"
          >
          <template v-slot:actions>
            <v-btn
                class="ms-auto"
                text="Ok"
                @click="dialog = false"
                ></v-btn>
          </template>
      </v-card>
    </v-dialog>

  </v-container>

</template>
<script setup lang="ts">
  import { problems } from "../problems.js";
  import { ref } from 'vue';

  const dialog = ref(false);
  const search = ref ('');
  const headers = ref([
      { align: 'start', key: 'code', title: 'Code'},
      { key: 'name', title: 'Name'},
      { key: 'local', title: 'From' },
      { key: 'tags', title: 'Tags' },
      { key: 'dificulty', title: 'Dificulty' },
  ])
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


  function getColorDif(rating) {
      if (rating < 1200) return 'gray'
      else if (rating < 1400) return 'green'
      else if (rating < 1600) return 'cyan'
      else if (rating < 1900) return 'blue'
      else if (rating < 2400) return 'yellow'
      else return 'red'
  }

  function getColorTag(tag) {
      return tags.get(tag);
  }

  function dl(){
      const filename = 'problems.js';
      const jsonStr = JSON.stringify(problems);
      let element = document.createElement('a');
      element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(jsonStr));
      element.setAttribute('download', filename);
      element.style.display = 'none';
      document.body.appendChild(element);
      element.click();
      document.body.removeChild(element);
  }
</script>
