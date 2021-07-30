<template>
  <v-card tile color="grey lighten-3">
    <v-toolbar card color="teal" dark>
      <v-btn icon @click="closeDialog()" dark>
        <v-icon>close</v-icon>
      </v-btn>
      <v-toolbar-title>Chiamate</v-toolbar-title>
    </v-toolbar>
    <v-card-text class="fill-height">
      <v-card class="fill-height">
        <v-card-title class="pa-1 pr-3">
          <v-spacer></v-spacer>
          <v-spacer></v-spacer>
          <v-spacer></v-spacer>
          <v-text-field
            append-icon="search"
            label="Ricerca"
            single-line
            v-model="search"
          ></v-text-field>
        </v-card-title>
        <v-data-table 
          :headers="headers"
          :items="calls"
          :search="search"
          item-key="createdDate"
          :rows-per-page-items="optionsRows"
        >
          <template slot="items" slot-scope="props">
            <td class="text-xs-center">{{ props.item.callHistory.RemoteNumber }}</td>
            <td class="text-xs-center"><a target="_blank" :href="getURL(props.item)">{{ props.item.codec.systemName }}</a></td>
            <td class="text-xs-center">{{ props.item.codec.ipAddress }} <br> {{ props.item.codec.macAddress }}</td>
            <td class="text-xs-center">{{ getDate(props.item.callHistory.StartTime) }}</td>
            <td class="text-xs-center">{{ getMinutes(props.item.callHistory.Duration) }}</td>
            <td class="text-xs-center">{{ props.item.callHistory.Direction }}</td>
            <td class="text-xs-center">{{ props.item.callHistory.Protocol }}</td>
            <td class="text-xs-center">{{ props.item.callHistory.RoomAnalytics.PeopleCount }}</td>
            <td class="text-xs-center">{{ props.item.rate || '/' }}</td>
            <td class="text-xs-center">{{ props.item.feedbacks || '/' }}</td>
            <td class="text-xs-center">
              <v-btn fab dark small color="teal" @click="showDialog(props.item.codec)">
                <v-icon dark>settings</v-icon>
              </v-btn>
            </td>
          </template>
          <v-alert slot="no-results" :value="true" color="error" icon="warning">
            La ricerca per "{{ search }}" non ha prodotto alcun risultato.
          </v-alert>
        </v-data-table>
      </v-card>
    </v-card-text>
    <v-dialog v-model="dialogDiagnostics" max-width="600px">
      <Diagnostics :codec="codecSelected" v-if="codecSelected"/>
    </v-dialog>
  </v-card>
</template>

<script>
import moment from 'moment'
import Diagnostics from './Diagnostics'

export default {
  data () {
    return {
      search: '',
      dialogDiagnostics: false,
      codecSelected: null,
      headers: [
        { text: 'URI Chiamata', value: 'callHistory.RemoteNumber', align: 'center' },
        { text: 'Codec utilizzato', value: 'codec.systemName', align: 'center' },
        { text: 'Indirizzo IP & MAC', value: 'codec.ipAddress', align: 'center' },
        { text: 'Data/ora chiamata', value: 'callHistory.StartTime', align: 'center' },
        { text: 'Durata (min.)', value: 'callHistory.Duration', align: 'center' },
        { text: 'Direzione chiamata', value: 'callHistory.Direction', align: 'center' },
        { text: 'Protocollo', value: 'callHistory.Protocol', align: 'center' },
        { text: 'Persone Presenti', value: 'callHistory.RoomAnalytics.PeopleCount', align: 'center' },
        { text: 'Note di chiamata', value: 'rate', align: 'center' },
        { text: 'Commenti', value: 'feedbacks', align: 'center' },
        { text: 'Diagnostica', value: 'diagnostics', align: 'center' }
      ],
      optionsRows: [10, 25]
    }
  },
  components: {
    Diagnostics
  },
  props: ['calls'],
  methods: {
    closeDialog () {
      this.$emit('dialogCalls')
    },
    getURL(item) {
      return 'http://' + item.codec.ipAddress
    },
    getDate(value) {
      return moment(value).format("Do MMMM YYYY HH:mm:ss")
    },
    getMinutes(value) {
      return Math.round(value / 60)
    },
    showDialog(codec) {
      this.codecSelected = codec
      this.dialogDiagnostics = true
    }
  }
}
</script>
