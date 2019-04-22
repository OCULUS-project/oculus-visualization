<template>
    <div id="searchComponent">
        <v-layout row wrap>
            <v-flex xs6>
                <v-card>
                    <v-card-title>
                        JOBS
                        <v-spacer></v-spacer>
                        <v-text-field v-model="searchJob" append-icon="search" label="Search" single-line hide-details></v-text-field>
                    </v-card-title>
                    <v-data-table v-if="jobs" :headers="headers" :items="jobs" :search="searchJob" class="elevation-1" :disable-initial-sort="true" :rows-per-page-items="[5, 10, 25, { text: '$vuetify.dataIterator.rowsPerPageAll', value: -1}]">
                        <template v-slot:items="props">
                            <tr :class="{ grey: props.item.id == activeJob }" @click="getFacts(props.item)">
                                <td>{{ props.item.owner }}</td>
                                <td>{{ props.item.patient }}</td>
                                <td v-if="props.item.status == 'DONE'" class="green--text"><strong>{{ props.item.status }}</strong></td>
                                <td v-else>{{ props.item.status }}</td>
                                <td class="px-1">{{ props.item.created }}</td>
                                <td class="px-1">{{ props.item.updated }}</td>
                                <td><v-btn fab small outline color="black" :to="'/facts/'+props.item.id" @click="getFacts(props.item)"><v-icon dark>fas fa-angle-double-right</v-icon></v-btn></td>
                            </tr>
                        </template>
                    </v-data-table>
                </v-card>
            </v-flex>
            <v-flex xs6 v-if="facts[0]">
                <v-card>
                    <v-card-title>
                        Facts
                        <v-spacer></v-spacer>
                        <v-text-field v-model="searchFacts" append-icon="search" label="Search" single-line hide-details></v-text-field>
                    </v-card-title>
                    <v-data-table :headers="factsHeaders" :items="facts" :search="searchFacts" class="elevation-1" :disable-initial-sort="true" :rows-per-page-items="[5, 10, 25, { text: '$vuetify.dataIterator.rowsPerPageAll', value: -1}]">
                        <template v-slot:items="props">
                            <td>{{ props.item.conjunction }}</td>
                            <td>{{ props.item.head }}</td>
                            <td>{{ props.item.grfIrf.grf }}</td>
                            <td>{{ props.item.grfIrf.irf }}</td>
                        </template>
                    </v-data-table>
                </v-card>
            </v-flex>
        </v-layout>
    </div>
</template>

<script lang="js">
import {Vue, Component, Prop} from 'vue-property-decorator';
import axios from 'axios';
import moment from 'moment';

export default Vue.extend({
  name: 'AllJobs',
  data: () => {
      return {
          searchJob: null,
          searchFacts: '',
          headers: [
              { text: 'Owner', align: 'left', sortable: true, value: 'owner' },
              { text: 'Patient', align: 'left', sortable: true, value: 'patient' },
              { text: 'Status', align: 'left', sortable: true, value: 'status' },
              { text: 'Created', align: 'left', sortable: true, value: 'created' },
              { text: 'Updated', align: 'left', sortable: true, value: 'updated' },
              { text: 'Facts', align: 'left', sortable: false, value: 'btn' },
          ],
          factsHeaders: [
              { text: 'Conjunction', align: 'left', sortable: true, value: 'conjunction' },
              { text: 'Head', align: 'left', sortable: true, value: 'head' },
              { text: 'grf', align: 'left', sortable: true, value: 'grfIrf.grf' },
              { text: 'irf', align: 'left', sortable: true, value: 'grfIrf.irf' },
          ],
          jobs: null,
          facts: { id: '' },
          activeJob: null,
      };
  },
  methods: {
    getAllJobs() {
        localStorage.setItem('query', this.query);
        // Request Settings
        const settings = {
            async: true,
            // crossDomain: true,
            url: 'http://jrie.eu:4500/db/jobs/all',
            method: 'GET',
        };

        axios(settings).then((response) => {
            this.jobs = response.data;
            for (const job of this.jobs) {
                job.created = moment.unix(job.created).format('YYYY-MM-DD HH:mm:ss');
                job.updated = moment.unix(job.updated).format('YYYY-MM-DD HH:mm:ss');
            }
        });
    },
    getFacts(item) {
        this.activeJob = item.id;
        this.facts = item.facts;
    },
  },
  mounted() {
      this.getAllJobs();
  },
});
</script>

<style scope lang="scss">
    img {
        margin-top: 5px;
    }
    .arrow-position {
        max-height: 64px;
        margin-right: 10px;
    }
    .less-margin-top {
        margin-top: -15px;
    }
</style>

