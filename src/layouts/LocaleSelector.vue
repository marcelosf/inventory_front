<template>

    <v-form v-model="valid">

        <v-container grid-list-lg>

            <v-layout row wrap>

                <v-flex xs12 sm4 md4 lg4>

                    <v-select
                            label="Bloco"
                            v-bind:items="locales"
                            :rules="buildRule"
                            item-text="build"
                            item-value="id"
                            v-model="build"
                            persistent-hint
                            autocomplete
                            required
                    ></v-select>

                </v-flex>

                <v-flex xs12 sm4 md4 lg4>

                    <v-select
                            label="Andar"
                            v-bind:items="floorItems"
                            :rules="floorRule"
                            item-text="floor"
                            item-value="id"
                            v-model="floor"
                            persistent-hint
                            autocomplete
                            required
                    ></v-select>

                </v-flex>

                <v-flex xs12 sm4 md4 lg4>

                    <v-select
                            label="Sala"
                            v-bind:items="localeItems"
                            :rules="roomRule"
                            item-text="name"
                            item-value="id"
                            v-model="room"
                            persistent-hint
                            autocomplete
                            required
                    ></v-select>

                </v-flex>

            </v-layout>

        </v-container>


    </v-form>


</template>

<script>
  import {Locales} from '@/resources/Locales';
  import {Filter} from '@/filters/Filter';

  export default {

    created () {

      Locales.list((items) => {

        this.locales = items;

      });

    },

    data () {

      return {

        locales: [],

        build: '',

        buildRule: [(value) => !!value || 'Please, select an option.'],

        floor: '',

        floorRule: [(value) => !!value || 'Please, select an option.'],

        room: '',

        roomRule: [(value) => !!value || 'Please, select an option.'],

        valid: false

      }

    },

    computed: {

      floorItems () {

        let build = this.locales[(this.build - 1)];

        if (build) {

          let floors = Filter.byParameterKey(build.floor, 'floor', this.locales);

          this.dispatchUpdateEvent();

          return floors;

        }

        return [];

      },

      localeItems () {

        let locale = this.locales[(this.floor - 1)];

        if (locale) {

          let locales = Filter.byParameterKey(locale.name, 'name', this.locales);

          this.dispatchUpdateEvent();

          return locales;

        }

        return [];

      }

    },

    methods: {

      dispatchUpdateEvent () {

        this.$emit('update', {build: this.build, floor: this.floor, room: this.room, valid: this.valid});

      }

    }

  }

</script>