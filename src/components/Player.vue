<template>
<div class="player">
  <v-container fluid grid-list-xs text-xs-center>
    <v-layout row wrap justify-start>
      <v-flex xs12 ma-1 pa-0>
        <span>Public area</span>
      </v-flex>
    </v-layout>
    <v-layout row nowrap justify-start class="material" >
      <draggable element="div" :list="arena" :move="beforeMove" @end="onEnd" :animation=300 :options="{group:'cards'}"
      v-for="(card, index) in arena" :key="index">
      <v-flex md3 ma-0 pa-0 class="lable" 
      v-if="card.suit=='label'">
          <span>Dragging<br /> Card Space→</span>
      </v-flex>
      <v-flex v-else xs2 ma-1 pa-0>
          <div class="card">
            {{card.suit}} {{card.number}}
          </div>
      </v-flex>
      </draggable>
    </v-layout>
  </v-container>
    <br />
  <v-container fluid grid-list-xl>
    <v-layout row wrap justify-start>
      <v-flex xs10 ma-1 pa-0 class="material">
        <span>Private area</span>
        <draggable :list="hand" :move="beforeMove" @end="onEnd" :animation=300  :group="cards">
          <div v-for="(card, index) in hand" :key="index">
            <span class="lable" v-if="card.suit=='label'">
              [Dragging Card Space]
            </span>
            <div v-else class="card">
              {{card.suit}} {{card.number}}
            </div>
          </div>
        </draggable>
      </v-flex>
    </v-layout>
  </v-container>
    <div class="flex" v-show="showButtons">
      <v-btn @click="hit">Hit</v-btn>
      <v-btn @click="stand">Stand</v-btn>
    </div>

</div>
</template>

<script>
import draggable from "vuedraggable";

import pick from '../utils/deck'
import calc from '../utils/calc'
import Card from './Card'

export default {
  name: 'player',
  components: { draggable, Card },
  props: ['showButtons'],
  data () {
    return {
      hand: [ { suit: "label"} ],
      arena: [{ suit: "label"}],
      result: 0,
    }
  },
  created: function () {
    this.hand.push(pick());
    this.hand.push(pick());
    this.hand.push(pick());
    this.hand.push(pick());
    this.arena.push(pick());
    this.arena.push(pick());
    this.arena.push(pick());
    this.result = calc(this.hand);
  },
  methods: {
    hit () {
      this.hand.push(pick());
      this.result = calc(this.hand);
    },
    stand () {
      this.$emit('stand', this.result)
    },
    beforeMove: function(evt) {
      return evt.draggedContext.element.name !== 'Drag Area';
    },
    onEnd: function(evt) {
      console.log(evt);
    },
  },
  watch: {
    result: function (newValue, oldValue) {
      if (newValue === 'Bust') {
        this.$emit('stand', newValue)
      }
    }
  }
}
</script>