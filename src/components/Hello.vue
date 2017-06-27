<template>
  <div>
    <h1>{{ msg }}</h1>
    <table>
    <tr><th colspan="2">User Support</th></tr>
    <tr v-for="record in userSupport">
      <td v-if="record.escalation_level===1">Level 1</td>
      <td>{{record.user.summary}}</td>
      <td>{{record.start | formatDate}} - {{record.start | formatDate}}</td>
    </tr>
    <tr><th colspan="2">Platform Support</th></tr>
    <tr v-for="record in platformSupport">
      <td v-if="record.escalation_level===1">Level 1</td>
      <td v-if="record.escalation_level===2">Level 2</td>
      <td>{{record.user.summary}}</td>
      <td>{{record.start | formatDate}} - {{record.start | formatDate}}</td>
    </tr>
    <tr><th colspan="2">Descriptions Team Platform</th></tr>
    <tr v-for="record in adhdSupport">
      <td v-if="record.escalation_level===1">Level 1</td>
      <td v-if="record.escalation_level===2">Level 2</td>
      <td>{{record.user.summary}}</td>
      <td>{{record.start | formatDate}} - {{record.start | formatDate}}</td>
    </tr>
    </table>
  </div>
</template>

<script>

const apiURL = 'https://api.pagerduty.com/oncalls?time_zone=Europe/Prague&earliest=true'
const token = 'Token token=' + process.env.PD_KEY
const sortBy = require('lodash.sortby')

export default {
  name: 'hello',
  data () {
    return {
      msg: 'Dashboard Test Application',
      response: null
    }
  },
  created: function () {
    this.fetchData()
  },
  filters: {
    formatDate: function (v) {
      return v.replace(/T|Z/g, ' ').replace(/\+\d+:\d+/g, '').slice(0, -3)
    }
  },
  computed: {
    userSupport: function () {
      if (this.response) {
        return this.response.filter(function (record) {
          return record.escalation_policy.summary === 'User Support On-Call' &&
            record.escalation_level === 1
        })
      }
    },
    platformSupport: function () {
      if (this.response) {
        return this.response.filter(function (record) {
          return (record.escalation_policy.summary === 'PlatformDefault' && (
            record.escalation_level === 1 ||
            record.escalation_level === 2))
        })
      }
    },
    adhdSupport: function () {
      if (this.response) {
        return this.response.filter(function (record) {
          return (record.escalation_policy.summary === 'Descriptions Team Platform' && (
            record.escalation_level === 1 ||
            record.escalation_level === 2))
        })
      }
    }
  },
  methods: {
    fetchData: function () {
      var xhr = new XMLHttpRequest()
      var returnedData
      var self = this
      xhr.open('GET', apiURL)
      xhr.setRequestHeader('Authorization', token)
      xhr.setRequestHeader('Accept', 'application/vnd.pagerduty+json;version=2')
      xhr.onload = function () {
        returnedData = JSON.parse(xhr.responseText)
        self.response = sortBy(returnedData.oncalls, [function (o) { return o.escalation_level }])
      }
      xhr.send()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

table {
  margin: 1em 0;
  min-width: 300px;
}

table tr {
  border-top: 1px solid #ddd;
  border-bottom: 1px solid #ddd;
}

table td:first-child {
  padding-top: .5em;
}

table td:last-child {
  padding-bottom: .5em;
}

table td:before {
  content: attr(data-th) ": ";
  font-weight: bold;
  width: 6.5em;
  display: inline-block;
}

@media (min-width: 480px) {
  table td:before {
    display: none;
  }
}

table th, table td {
  text-align: left;
}

media (min-width: 480px) {
  table th, table td {
    display: table-cell;
    padding: .25em .5em;
  }
  table th:first-child, table td:first-child {
    padding-left: 0;
  }
  table th:last-child, table td:last-child {
    padding-right: 0;
  }
}
a {
  color: #42b983;
}

table {
  background: #34495E;
  color: #fff;
  border-radius: .4em;
  overflow: hidden;
  margin: 0 auto;
}

table tr {
  border-color: #46637f;
}
table th, table td {
  margin: .5em 1em;
}
@media (min-width: 480px) {
  table th, table td {
    padding: 1em !important;
  }
}
table th, table td:before {
  color: #dd5;
}
h1 {
  font-weight: normal;
  letter-spacing: -1px;
  color: #34495E;
}
body {
  padding: 0 2em;
  font-family: Montserrat, sans-serif;
  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
  color: #444;
  background: #eee;
}
</style>
