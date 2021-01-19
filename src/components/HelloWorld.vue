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
              {{vitalSigns}}
            </v-list-item-subtitle>
          </v-list-item-content>

          <v-list-item-avatar
            tile
            size="80"
            color="grey"
          ></v-list-item-avatar>
        </v-list-item>
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
      vitalSigns: []
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
        const baseURI = 'http://hapi.fhir.org/baseR4/Patient/'+patientId+'/Observation&category=vital-signs'
        this.$http.get(baseURI)
        .then((result) => {
          this.vitalSigns = result.data
        })
      }

    }
  }
</script>
