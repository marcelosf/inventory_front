<template>

    <div>

        <v-layout row wrap>

            <v-flex xs12 sm12 md12 lg12>

                <v-card flat>

                    <v-card-title primary-title>

                        <div v-if="hasOrder" class="title">Observation</div>

                        <div v-if="!hasOrder" class="title">No Order Found</div>

                        <v-spacer></v-spacer>

                        <span class="hidden-xs-only">

                            <v-btn flat color="primary" @click.stop="openReportDialog">

                                Reports

                                <v-icon right>assignment</v-icon>

                            </v-btn>

                            <v-btn flat color="primary" @click.stop="openNewOrderDialog">

                                New Order

                                <v-icon right>add</v-icon>

                            </v-btn>

                        </span>

                        <span class="hidden-sm-and-up">

                            <v-btn icon color="primary" @click.stop="openReportDialog">

                                <v-icon>assignment</v-icon>

                            </v-btn>

                            <v-btn icon color="primary" @click.stop="openNewOrderDialog">

                                <v-icon>add</v-icon>

                            </v-btn>

                        </span>

                    </v-card-title>

                    <v-card-text>

                        <blockquote class="blockquote">

                            {{ order ? order.observation : ''}}

                        </blockquote>

                    </v-card-text>

                </v-card>

            </v-flex>

        </v-layout>

        <div v-show="hasOrder">

            <v-layout row wrap>

                <v-flex xs12 sm4 md4 lg4>

                    <v-card flat>

                        <v-card-text>

                            <date-picker label="Start date" v-model="start_date">
                            </date-picker>

                        </v-card-text>

                    </v-card>

                </v-flex>

                <v-flex xs12 sm4 md4 lg4>

                    <v-card flat>

                        <v-card-text>

                            <date-picker label="End date" v-model="end_date">
                            </date-picker>

                        </v-card-text>

                    </v-card>

                </v-flex>

                <v-flex xs12 sm4 md4 lg4>

                    <v-card flat>

                        <v-card-text>

                            <v-select
                                    label="Status"
                                    v-bind:items="statusItems"
                                    item-value="status"
                                    item-text="text"
                                    v-model="status"
                            >
                            </v-select>

                        </v-card-text>

                    </v-card>

                </v-flex>

                <v-flex xs12 sm12 md12 lg12>

                    <v-card flat>

                        <v-card-text>

                            <v-select
                                    v-bind:items="techniciansItems"
                                    item-value="id"
                                    item-text="name"
                                    v-model="technicians"
                                    label="Technicians"
                                    multiple
                                    chips
                            >
                            </v-select>

                        </v-card-text>

                    </v-card>

                </v-flex>

                <v-flex xs12 sm4 md4 lg4>

                    <v-card flat color="grey lighten-4">

                        <v-card-title primary-title>

                            <div class="title">Technical Area</div>

                        </v-card-title>

                        <v-card-text>

                            <blockquote class="blockquote">

                                {{ order ? order.technical.name : null }}

                            </blockquote>

                        </v-card-text>

                    </v-card>

                </v-flex>

                <v-flex>

                    <v-card flat>

                        <v-card-title>

                            <div class="title">EPIs</div>

                        </v-card-title>

                        <v-card-text>

                            <epis v-model="order" :items="epiItems" @updated="_emitOrder">

                            </epis>

                        </v-card-text>

                    </v-card>

                </v-flex>

                <v-dialog v-model="dialog" fullscreen :transition="transition" :overlay="false" scrollable>

                    <reports v-model="dialog" :order="order" v-show="reportIsSelected">

                    </reports>

                    <new-order v-model="dialog" v-show="newOrderIsSelected" @created="showCreatedOrderMessage">

                    </new-order>

                </v-dialog>

            </v-layout>

        </div>

        <messages :message="snackbar.message" v-model="snackbar.toggle">

        </messages>

    </div>

</template>

<script>
  import DatePicker from '@/layouts/DatePicker';
  import {UserResource} from '@/resources/UserResource';
  import Epis from './Service.Orders.Show.Epis';
  import Reports from './Service.Order.Reports';
  import OderCreate from './Service.Order.Create';
  import {MaintenanceResource} from '@/resources/MaintenanceResource';
  import SnackBar from '@/layouts/SnackBar';

  const REPORT_SELECTOR = 'report';
  const NEW_ORDER_SELECTOR = 'newOrder';

  export default {

    props: ['value'],

    created () {

      this._initialize();

    },

    data () {

      return {

        statusItems: [

          {status: 'autorizado', text: 'autorizado'},
          {status: 'andamento', text: 'andamento'},
          {status: 'resolvido', text: 'resolvido'},
          {status: 'cancelado', text: 'cancelado'},
          {status: 'aguardando_info', text: 'aguardando_info'},
          {status: 'aguardando_material', text: 'aguardando_material'}

        ],

        techniciansItems: [],

        order: null,

        epiItems: [],

        dialog: false,

        dialogSelector: null,

        transition: 'dialog-bottom-transition',

        snackbar: {

          message: '',

          toggle: false

        }

      }

    },

    watch: {

      value (value) {

        this.order = value;

      }

    },

    computed: {

      start_date: {

        get () {

          if (this.value) {

            return this.value.start_date;

          }

          return null;

        },

        set (value) {

          if (this._isUpdated(value, this.order.start_date)) {

            this.order.start_date = value;

            this._emitOrder();

          }

        }

      },

      end_date: {

        get () {

          if (this.value) {

            return this.value.end_date;

          }

          return null;

        },

        set (value) {

          if (this._isUpdated(value, this.order.end_date)) {

            this.order.end_date = value;

            this._emitOrder();

          }

        }

      },

      technicians: {

        get () {

          if (this.value) {

            return this.value.technicians;

          }

        },

        set (value) {

          if (this._isUpdated(value, this.order.technicians)) {

            this.order.technicians = value;

            this._emitOrder();

          }

        }

      },

      status: {

        get () {

          if (this.value) {

            return this.value.status;

          }

          return null;

        },

        set (value) {

          if (this._isUpdated(value, this.order.status)) {

            this.$set(this.order, 'status', value);

            this._emitOrder();

          }

        }

      },

      reportIsSelected () {

        return this.dialogSelector === REPORT_SELECTOR;

      },

      newOrderIsSelected () {

        return this.dialogSelector === NEW_ORDER_SELECTOR;

      },

      hasOrder () {

        return !!this.order;

      }

    },

    methods: {

      _initialize () {

        this._loadTechnicians();

        this._loadEpiItems();

      },

      _loadTechnicians () {

        UserResource.list((response) => {

          this.techniciansItems = response.data;

        });

      },

      _loadEpiItems () {

        MaintenanceResource.listEpis((epis) => {

          this.epiItems = epis;

        });

      },

      _emitOrder () {

        this.$emit('input', this.order);

        this.$emit('updated');

      },

      _isUpdated (value1, value2) {

        return value1 !== value2;

      },

      openReportDialog () {

        this.dialogSelector = REPORT_SELECTOR;

        this.dialog = true;

      },

      openNewOrderDialog () {

        this.dialogSelector = NEW_ORDER_SELECTOR;

        this.dialog = true;

      },

      showCreatedOrderMessage (response) {

        this.showMessages(response.message);

      },

      showMessages (message) {

        this.snackbar.message = message;

        this.snackbar.toggle = true;

      }

    },

    components: {

      'date-picker': DatePicker,
      'epis': Epis,
      'reports': Reports,
      'new-order': OderCreate,
      'messages': SnackBar

    }

  }

</script>