<template>
  <v-container class = "bg-variant">
    <v-container class="" fluid>
      <v-responsive class="mx-auto text-left">
        <p class="font-weight-medium text-primary">Competitive Programing Problem Enumerator</p>
        <p class="font-weight-bold text-sm-h2 text-h4 mt-2'"> CoPPE</p>
      </v-responsive>
    </v-container>
    <v-card flat >
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

      <v-data-table
          :headers="headers"
          :items="problems"
          :search="search"
          >

          <template #item.tags="{ item }">
            <v-chip-group>
              <template v-for="i in item.tags" :key="i">
                <v-chip> {{ i }} </v-chip>
              </template>
            </v-chip-group>
          </template>

      </v-data-table>
      <v-container>
        <v-btn icon @click="dialog = true"> <v-icon>mdi-plus</v-icon> </v-btn>

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

        <v-btn icon @click="dl()">
          <v-icon>mdi-export</v-icon>
        </v-btn>
      </v-container>
    </v-card>

  </v-container>
</template>
<script setup lang="ts">
  import { problems } from "../problems.js";
  import { ref } from 'vue';

  const dialog = ref(false);
  const search = ref ('');
  const headers = ref([
      {
        align: 'start',
        key: 'code',
        title: 'Code',
      },
      { key: 'local', title: 'From' },
      { key: 'tags', title: 'Tags' },
  ])



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
