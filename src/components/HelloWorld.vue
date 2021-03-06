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
          <v-list-item-content >
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
        outlined
      >
        <v-toolbar
          color="cyan"
          dark
          flat
        >
          <v-spacer></v-spacer>
          <v-toolbar-title>REMOTE PATIENT MONITORING - OBSERVATIONS</v-toolbar-title>
          <v-spacer></v-spacer>
        </v-toolbar>
        <v-row>
          <v-col>
            <v-btn
              depressed
              color="primary"
              @click="setTemperature"
            >
              Body Temperature
            </v-btn>
          </v-col>
          <v-col>
            <v-btn
              depressed
              color="primary"
              @click="setWeight"
            >
              Body Weight
            </v-btn>
          </v-col>
          <v-col>
            <v-btn
              depressed
              color="primary"
              @click="setRespiratoryRate"
            >
              Respiratory Rate
            </v-btn>
          </v-col>
          <v-col>
            <v-btn
              depressed
              color="primary"
              @click="setHeartRate"
            >
              Heart Rate
            </v-btn>
          </v-col>
        </v-row>
      </v-card>
      <v-form
        ref="form"
      >
        <v-card
          class="mx-auto"
          max-width="1080"
          outlined>
          <v-card-text class="bold text-center">
            <span>PATIENT RPM OBSERVATIONS</span>
          </v-card-text>
          <v-row>
            <v-col>
              <v-select
                :items="observationTypes"
                v-model="selectedObservation"
                label="Choose Observation"
                dense
              ></v-select>
            </v-col>
            <v-col>
              <v-text-field
                v-model="newValue"
                label="Enter Value"
                regular
                dense
              />
            </v-col>
            <v-col cols="2">
              <v-btn
                depressed
                color="secondary"
                @click="createPatientObservationRespiratoryRate(newObject)"
              >
                Add Record
              </v-btn>
            </v-col>
          </v-row>
        </v-card>
      </v-form>
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
      bodyTemperature: [],
      bodyWeight: [],
      respiratoryRate: [],
      heartRate: [],


      // Pagination
      page: 1,
      pageCount: 0,
      itemsPerPage: 25,

      // Dashboard
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
      ],

      // Tabs

      tab: null,
      rpmItems: [],
      items: [
        { text: 'Body Temperature', value: 'temp' },
        { text: 'Body Weight', value: 'calories two' },
        { text: 'Respiratory Rate', value: 'calories three' },
        { text: 'Heart rate', value: 'calories four' },
      ],

      observationTypes: ['Temperature', 'Weight', 'Respiratory Rate', 'Heart Rate'],
      selectedObservation: 'Temperature',
      newValue: null,
      // JSON Objects
      temperatureCode: {
              "coding": [
                  {
                      "system": "http://loinc.org",
                      "code": "8310-5",
                      "display": "Body temperature"
                  }
              ],
              "text": "Body temperature"
            },
      temperatureQuantity: {
              "value": 36.5,
              "unit": "C",
              "system": "http://unitsofmeasure.org",
              "code": "Cel"
            },
      weightCode: {
              "coding": [
                  {
                      "system": "http://loinc.org",
                      "code": "29463-7",
                      "display": "Body Weight"
                  }
              ],
              "text": "Body weight"
            },
      weightQuantity: {
              "value": 82.5,
              "unit": "kg",
              "system": "http://unitsofmeasure.org",
              "code": "kg"
            },
      respiratoryCode: {
              "coding": [
                  {
                      "system": "http://loinc.org",
                      "code": "9279-1",
                      "display": "Respiratory rate"
                  }
              ],
              "text": "Respiratory rate"
            },
      respiratoryQuantity: {
              "value": 10,
              "unit": "/min",
              "system": "http://unitsofmeasure.org",
              "code": "/min"
            },
      heartCode: {
              "coding": [
                  {
                      "system": "http://loinc.org",
                      "code": "8867-4",
                      "display": "Heart rate"
                  }
              ],
              "text": "Heart rate"
            },
      heartQuantity: {
              "value": 10,
              "unit": "/min",
              "system": "http://unitsofmeasure.org",
              "code": "/min"
            },

      newObject: {
          "resourceType": "Observation",
          "meta": {
              "tag": [
                  {
                      "code": "HDR Unit Testing"
                  }
              ]
          },
          "status": "final",
          "category": [
              {
                  "coding": [
                      {
                          "system": "http://terminology.hl7.org/CodeSystem/observation-category",
                          "code": "vital-signs",
                          "display": "vital-signs"
                      }
                  ]
              }
          ],
          "code": {
              "coding": [
                  {
                      "system": "http://loinc.org",
                      "code": "9279-1",
                      "display": "Respiratory rate"
                  }
              ],
              "text": "Respiratory rate"
          },
          "subject": {
              "reference": "Patient/1741899"
          },
          "effectiveDateTime": "2021-01-19T10:13:12+05:30",
          "issued": "2021-01-19T10:13:12+05:30",
          "valueQuantity": {
              "value": 10,
              "unit": "/min",
              "system": "http://unitsofmeasure.org",
              "code": "/min"
          }
        }
    }),
    mounted () {
      const patientId = 1741899
      const temp_code = '8310-5'
      const weight_code = '29463-7'
      const resp_code = '9279-1'
      const heart_code = '8867-4'

      this.getPatient(patientId)
      this.getPatientObservation(patientId)
      this.getPatientObservationTemperature(patientId, temp_code)
      this.getPatientObservationWeight(patientId, weight_code)
      this.getPatientObservationRespiratoryRate(patientId, resp_code)
      this.getPatientObservationHeartRate(patientId, heart_code)
      // this.setRespiratoryRate()

      // this.rpmItems.push({ text: 'Body Temperature', value: this.bodyTemperature, threshold: 100 })
      //this.rpmItems.push({ text: 'Body Weight', value: this.bodyWeight, threshold: 90 })
    },
    methods: {
      setTemperature: function () {
        this.vitalSigns = this.bodyTemperature
      },
      setWeight: function () {
        this.vitalSigns = this.bodyWeight
      },
      setRespiratoryRate: function () {
        this.vitalSigns = this.respiratoryRate
      },
      setHeartRate: function () {
        this.vitalSigns = this.heartRate
      },
      getPatient: function (patientId) {
        const baseURI = 'https://hapi.fhir.org/baseR4/Patient/'+patientId
        this.$http.get(baseURI)
        .then((result) => {
          this.patient = result.data
        })
      },
      getPatientObservationTemperature: function (patientId, code) {
        const baseURI = 'https://hapi.fhir.org/baseR4/Observation?patient='+patientId+'&category=vital-signs&code='+code
        this.$http.get(baseURI)
        .then((result) => {
          this.bodyTemperature = result.data.entry
        })
      },
      getPatientObservationWeight: function (patientId, code) {
        const baseURI = 'https://hapi.fhir.org/baseR4/Observation?patient='+patientId+'&category=vital-signs&code='+code
        this.$http.get(baseURI)
        .then((result) => {
          this.bodyWeight = result.data.entry
        })
      },
      getPatientObservationRespiratoryRate: function (patientId, code) {
        const baseURI = 'https://hapi.fhir.org/baseR4/Observation?patient='+patientId+'&category=vital-signs&code='+code
        this.$http.get(baseURI)
        .then((result) => {
          this.respiratoryRate = result.data.entry
        })
      },
      getPatientObservationHeartRate: function (patientId, code) {
        const baseURI = 'https://hapi.fhir.org/baseR4/Observation?patient='+patientId+'&category=vital-signs&code='+code
        this.$http.get(baseURI)
        .then((result) => {
          this.heartRate = result.data.entry
        })
      },
      createPatientObservationRespiratoryRate: function (body) {
        if(this.newValue == null) {
          alert("Please Enter Value")
          return
        }
        var currentDate = new Date()
        this.newObject["issued"] = currentDate.toISOString()
        this.newObject["effectiveDateTime"] = currentDate.toISOString()
        if(this.selectedObservation == "Temperature") {
          this.newObject["code"] = this.temperatureCode
          this.newObject["valueQuantity"] = this.temperatureQuantity
        } else if(this.selectedObservation == "Weight") {
          this.newObject["code"] = this.weightCode
          this.newObject["valueQuantity"] = this.weightQuantity
        } else if(this.selectedObservation == "Respiratory Rate") {
          this.newObject["code"] = this.respiratoryCode
          this.newObject["valueQuantity"] = this.respiratoryQuantity
        } else if(this.selectedObservation == "Heart Rate") {
          this.newObject["code"] = this.heartCode
          this.newObject["valueQuantity"] = this.heartQuantity
        } else {
          alert("Invalid type")
        }
        this.newObject["valueQuantity"]["value"] = this.newValue
        const baseURI = 'https://hapi.fhir.org/baseR4/Observation'
        this.$http.post(baseURI, body, {
            headers: { 
                'Content-Type' : 'application/json' 
            }
        })
        .then((result) => {
          this.getPatientObservation(1741899)
          alert("Created Successfully")
        })
      },
      getPatientObservation: function (patientId) {
        const baseURI = 'https://hapi.fhir.org/baseR4/Observation?patient='+patientId
        this.$http.get(baseURI)
        .then((result) => {
          this.vitalSigns = result.data.entry
        })
      }

    }
  }
</script>
