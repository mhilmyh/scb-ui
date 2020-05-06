<template>
  <v-container>
    <v-row>
      <template v-for="(item, i) in summary">
        <v-col :key="item.name + i" cols="12" sm="6" md="3">
          <v-card color="accent" dark class="pa-5">
            <v-list-item two-line>
              <v-list-item-content>
                <v-list-item-title class="headline font-weight-black">
                  {{ item.value }}
                </v-list-item-title>
                <v-divider></v-divider>
                <v-list-item-subtitle class="body-2 text-capitalize">
                  {{ $translate('text.' + item.name) }}
                </v-list-item-subtitle>
              </v-list-item-content>
              <v-list-item-action>
                <v-icon large>{{ item.icon }}</v-icon>
              </v-list-item-action>
            </v-list-item>
          </v-card>
        </v-col>
      </template>
    </v-row>
    <v-row>
      <v-col>
        <v-card>
          <v-card-title class="title">
            <span class="text-uppercase primary--text font-weight-black"
              >Divisi Keuangan</span
            >
            <v-divider vertical class="mx-2"></v-divider>
            <span class="text-uppercase font-weight-black">
              {{ $translate('text.user') }}
            </span>
            <v-spacer></v-spacer>
            <v-text-field
              v-model="search"
              push-icon="mdi-magnify"
              :label="$translate('text.search', 'capitalize')"
              single-line
              hide-details
              solo
            ></v-text-field>
          </v-card-title>
          <v-card-text>
            <v-data-table :items="items" :headers="headers" :search="search">
              <template v-slot:item.id="{ item }">
                <v-btn
                  color="secondary"
                  text
                  small
                  @click.stop="popupUser(item)"
                  >{{ $translate('text.view') }}</v-btn
                >
              </template>
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-dialog v-model="modal.user" width="600px" persistent>
      <v-card>
        <v-card-title class="text-capitalize headline">
          {{ $translate('text.user') }}
        </v-card-title>
        <v-card-text>
          <v-simple-table>
            <template v-slot:default>
              <tbody>
                <tr>
                  <td class="caption font-weight-bold text-capitalize">
                    {{ $translate('text.name') }}
                  </td>
                  <td class="text-capitalize">{{ user.name }}</td>
                </tr>
                <tr>
                  <td class="caption font-weight-bold text-capitalize">
                    {{ $translate('text.username') }}
                  </td>
                  <td>{{ user.username }}</td>
                </tr>
                <tr>
                  <td class="caption font-weight-bold text-capitalize">
                    {{ $translate('text.email') }}
                  </td>
                  <td>{{ user.email }}</td>
                </tr>
                <tr>
                  <td class="caption font-weight-bold text-capitalize">
                    {{ $translate('text.division') }}
                  </td>
                  <td class="text-capitalize">{{ user.division }}</td>
                </tr>
                <tr>
                  <td class="caption font-weight-bold text-capitalize">
                    {{ $translate('text.position') }}
                  </td>
                  <td class="text-capitalize">{{ user.position }}</td>
                </tr>
                <tr>
                  <td class="caption font-weight-bold text-uppercase">
                    {{ $translate('text.nik') }}
                  </td>
                  <td>{{ user.nik }}</td>
                </tr>
                <tr>
                  <td class="caption font-weight-bold text-capitalize">
                    {{ $translate('text.address') }}
                  </td>
                  <td>{{ user.address }}</td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-card-text>
        <v-card-actions>
          <v-btn
            dark
            color="secondary"
            block
            @click.stop="modal.user = false"
            >{{ $translate('components.button.close') }}</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <snackbar-alert
      v-model="alert"
      :success="success"
      :messages="messages"
    ></snackbar-alert>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      alert: false,
      success: false,
      messages: '',
      search: '',
      summary: [],
      headers: [
        {
          text: `${this.$translate('text.name', 'capitalize')}`,
          value: 'name'
        },
        {
          text: `${this.$translate('text.username', 'capitalize')}`,
          value: 'username'
        },
        {
          text: `${this.$translate('text.email', 'capitalize')}`,
          value: 'email'
        },
        {
          text: `${this.$translate('text.division', 'capitalize')}`,
          value: 'division'
        },
        {
          text: `${this.$translate('text.action', 'capitalize')}`,
          value: 'id',
          sortable: false,
          align: 'center'
        }
      ],
      items: [],

      modal: {
        user: false
      },

      user: {}
    }
  },
  mounted() {
    this.getFormRequestCount()
    this.getFormSubmissionCount()
    this.getFormPettyCashCount()
    this.getAllUsers()
  },
  methods: {
    popupUser(user) {
      this.user = user
      this.modal.user = true
    },
    async getAllUsers() {
      try {
        const { users } = await this.$api('user', 'all')
        this.items = users
        this.summary.push({
          name: 'user',
          value: users.length,
          icon: 'mdi-account-circle'
        })
      } catch (e) {
        this.success = false
        this.messages = 'Terjadi kesalahan : ' + e.toString().slice(0, 10)
        this.alert = true
      }
    },
    async getFormRequestCount() {
      try {
        const response = await this.$api('request', 'count')
        this.summary.push({
          name: 'request',
          value: response.form_request_count,
          icon: 'mdi-newspaper'
        })
      } catch (e) {
        this.success = false
        this.messages = 'Terjadi kesalahan : ' + e.toString().slice(0, 10)
        this.alert = true
      }
    },
    async getFormSubmissionCount() {
      try {
        const response = await this.$api('submission', 'count')
        this.summary.push({
          name: 'submission',
          value: response.form_submission_count,
          icon: 'mdi-newspaper-variant-multiple'
        })
      } catch (e) {
        this.success = false
        this.messages = 'Terjadi kesalahan : ' + e.toString().slice(0, 10)
        this.alert = true
      }
    },
    async getFormPettyCashCount() {
      try {
        const response = await this.$api('petty', 'count')
        this.summary.push({
          name: 'petty_cash',
          value: response.form_petty_cash_count,
          icon: 'mdi-cash-multiple'
        })
      } catch (e) {
        this.success = false
        this.messages = 'Terjadi kesalahan : ' + e.toString().slice(0, 10)
        this.alert = true
      }
    }
  }
}
</script>