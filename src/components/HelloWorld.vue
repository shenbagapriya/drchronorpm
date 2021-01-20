<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <h1>Welcome to DrChrono Hackathon - Remote Patient Monitoring FHIR App</h1>
      </v-col>
    </v-row>
    <div>
      <v-card
        class="mx-auto"
        max-width="1080"
        outlined
      >
        <v-list-item three-line>
          <v-list-item-content>
            <!--div class="overline mb-4">
              PATIENT
            </div-->
            <v-list-item-title class="headline mb-1">
              {{patient.name[0].given[0]}}, {{patient.name[0].family}} 
            </v-list-item-title>
            <v-list-item-subtitle>
              <v-row>
                <v-col cols="2">Gender :</v-col>
                <v-col cols="4">{{patient.gender}}</v-col>
              </v-row>
              <v-row>
                <v-col cols="2">BirthDate :</v-col>
                <v-col cols="4">{{patient.birthDate}}</v-col>
              </v-row>
              <v-row>
                <v-col cols="2">Phone :</v-col>
                <v-col cols="4">{{patient.telecom[0].value}}</v-col>
              </v-row>
              <v-row>
                <v-col cols="2">Email :</v-col>
                <v-col cols="4">{{patient.telecom[0].value}}</v-col>
              </v-row>
              <v-row>
                <v-col cols="2">Address :</v-col>
                <v-col cols="4">{{patient.address[0].line[0]}}</v-col>
              </v-row>
              <v-row>
                <v-col cols="2"></v-col>
                <v-col cols="4">{{patient.address[0].city}}, {{patient.address[0].state}}, {{patient.address[0].country}} - {{patient.address[0].postalCode}}</v-col>
              </v-row>
            </v-list-item-subtitle>
          </v-list-item-content>

          <v-list-item-avatar
            tile
            size="80"
            color="grey"
          ></v-list-item-avatar>
        </v-list-item>
      </v-card>
      <v-card
        class="mx-auto"
        max-width="1080"
        outlined>
        <v-card-text class="bold text-center">
          <span>PATIENT RPM OBSERVATIONS</span>
        </v-card-text>
        <v-row>
          <v-col>
            <v-data-table
              :headers="headers"
              :items="vitalSigns"
              :page.sync="page"
              :items-per-page="itemsPerPage"
              hide-default-footer
              class="elevation-1"
              @page-count="pageCount = $event"
            >
              <template v-slot:item="{item}">
                <tr>
                  <td> {{item.resource.effectiveDateTime}} </td>
                  <td> {{item.resource.code.text}} </td>
                  <td> {{item.resource.code.coding[0].code}} </td>
                  <td> 
                    <span v-if="item.resource.valueQuantity">
                      <span :class="{'error': item.resource.valueQuantity.value>10}">
                      {{item.resource.valueQuantity.value}} {{item.resource.valueQuantity.unit}}
                      </span>
                    </span> 
                  </td>
                </tr>
              </template>
            </v-data-table>
            <div class="text-center pt-2">
              <v-pagination
                v-model="page"
                :length="pageCount"
                color="warning"
              />
            </div>
          </v-col>
        </v-row>
      </v-card>
    </div>
  </v-container>
</template>

<script>
  export default {
    name: 'DrChronoRPM',

    data: () => ({
      users: [],
      patient: null,
      vitalSigns: [],

      page: 1,
      pageCount: 0,
      itemsPerPage: 25,

      headers: [
        {
          text: 'Date Time',
          align: 'start',
          sortable: false,
          value: 'name',
        },
        { text: 'Code', value: 'calories' },
        { text: 'Cateogy', value: 'fat' },
        { text: 'Value', value: 'carbs' },
      ]

    }),
    mounted () {
      this.getPatient(1741899)
      this.getPatientObservation(1741899)
    },
    methods: {
      getPatient: function (patientId) {
        const baseURI = 'http://hapi.fhir.org/baseR4/Patient/'+patientId
        this.$http.get(baseURI)
        .then((result) => {
          this.patient = result.data
        })
      },
      getPatientObservation: function (patientId) {
        const baseURI = 'http://hapi.fhir.org/baseR4/Observation?patient='+patientId
        this.$http.get(baseURI)
        .then((result) => {
          this.vitalSigns = result.data.entry
        })
      }

    }
  }
</script>
