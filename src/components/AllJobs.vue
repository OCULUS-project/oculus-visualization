<template>
    <div id="searchComponent">
        <!-- <v-text-field label="Search jobs..." solo @keyup="searchJob" v-model="query">{{ query }}</v-text-field> -->
        
        <v-layout row wrap>
            <v-flex xs6>
                <v-data-table v-if="jobs" :headers="headers" :items="jobs" class="elevation-1" :disable-initial-sort="true" :rows-per-page-items="[5, 10, 25, { text: '$vuetify.dataIterator.rowsPerPageAll', value: -1}]">
                    <template v-slot:items="props">
                        <tr :class="{ grey: props.item.id == activeJob }" @click="getFacts(props.item)">
                            <td>{{ props.item.owner }}</td>
                            <td>{{ props.item.patient }}</td>
                            <td v-if="props.item.status == 'DONE'" class="green--text"><strong>{{ props.item.status }}</strong></td>
                            <td v-else>{{ props.item.status }}</td>
                            <td>{{ props.item.created }}</td>
                            <td>{{ props.item.updated }}</td>
                            <td><v-btn fab small outline color="black" :to="'/facts/'+props.item.id" @click="getFacts(props.item)"><v-icon dark>fas fa-angle-double-right</v-icon></v-btn></td>
                        </tr>
                    </template>
                </v-data-table>
            </v-flex>
            <v-flex xs6>
                <v-data-table v-if="facts[0]" :headers="factsHeaders" :items="facts" class="elevation-1" :disable-initial-sort="true" :rows-per-page-items="[5, 10, 25, { text: '$vuetify.dataIterator.rowsPerPageAll', value: -1}]">
                    <template v-slot:items="props">
                        <td>{{ props.item.conjunction }}</td>
                        <td>{{ props.item.head }}</td>
                        <td>{{ props.item.grfIrf.grf }}, {{ props.item.grfIrf.irf }}</td>
                    </template>
                </v-data-table>
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
          query: '',
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
              { text: 'head', align: 'left', sortable: true, value: 'head' },
              { text: 'grfIrf', align: 'left', sortable: false, value: 'grfIrf' },
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
    searchJob() {
        // TODO: Implement
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

