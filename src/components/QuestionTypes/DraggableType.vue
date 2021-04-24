<template>
  <span>
    <draggable
      ref="input"
      :list="question.options"
      class="list-group"
      ghost-class="ghost"
      v-on:keydown="onKeyDownListener"
      v-on:keyup="onKeyUpListener"
      @end="onChangeOther"
    >
      <div
        class="list-group-item"
        v-for="element in question.options"
        :key="element.name"
      >
        {{ element.choiceLabel() }}
      </div>
    </draggable>
  </span>
</template>

<script>
  /*
    Copyright (c) 2020 - present, DITDOT Ltd. - MIT Licence
    https://github.com/ditdot-dev/vue-flow-form
    https://www.ditdot.hr/en
  */

  import BaseType from './BaseType.vue'
  import { QuestionType } from '../../models/QuestionModel'
  import draggable from "vuedraggable";

  export default {
    extends: BaseType,
    name: QuestionType.Draggable,
    components: {
      draggable
    },
    computed: {
      answerLabel() {
        for (let i = 0; i < this.question.options.length; i++) {
          let option = this.question.options[i]

          if (option.choiceValue() === this.dataValue) {
            return option.choiceLabel()
          }
        }

        return this.question.placeholder
      }
    },
    created: function() {
      this.onChangeOther()
    },
    
     methods: {
      onKeyDownListener($event) {
        if ($event.key === 'ArrowDown' || $event.key === 'ArrowUp') {
          this.setAnswer(this.dataValue)
        } else if ($event.key === 'Enter' && this.hasValue) {
          this.focused = false
          this.blur()
        }
      },
      onKeyUpListener($event) {
        if ($event.key === 'Enter' && this.isValid()) {
          $event.stopPropagation()
          this._onEnter()
          this.$emit('next')
        }
      },
      onChangeOther() {
        let value = [];
        this.question.options.forEach(function(option) {
            value.push(option.choiceValue())
        })
        this.dataValue = value
        this.setAnswer(this.dataValue)
      },
    }
  }
</script>