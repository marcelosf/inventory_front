<template>

    <v-container fuid grid-list-lg>

        <v-layout row wrap justify-end>

            <v-flex xs12 sm6 md3 lg3 xl2 v-for="(card, i) in cards" :key="i">

                <v-card hover height="200px" :to="{name: card.list}">

                    <v-card-media :src="card.media" height="150px">

                        <span class="headline white--text pl-3" v-text="card.title"></span>

                    </v-card-media>

                    <v-card-actions>

                        <v-tooltip bottom v-if="card.add">

                            <v-btn  icon slot="activator" :to="{name: card.add}">

                                <v-icon>add</v-icon>

                            </v-btn>

                            <span>{{card.add_text}}</span>

                        </v-tooltip>

                        <v-tooltip bottom v-if="card.list">

                            <v-btn  icon slot="activator" :to="{name: card.list}">

                                <v-icon>list</v-icon>

                            </v-btn>

                            <span>{{card.list_text}}</span>

                        </v-tooltip>

                    </v-card-actions>

                </v-card>

            </v-flex>

        </v-layout>

    </v-container>

</template>

<script>
    export default {

       props: ['cards']

    }

</script>