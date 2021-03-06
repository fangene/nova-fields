<template>
  <r64-default-field
    :hide-field="hideField"
    :field="field"
    :hide-label="hideLabelInForms"
    :field-classes="fieldClasses"
    :wrapper-classes="wrapperClasses"
    :label-classes="labelClasses"
  >
    <template slot="field">
      <input
        :id="field.name"
        :dusk="field.attribute"
        :type="inputType"
        :min="inputMin"
        :max="inputMax"
        :step="inputStep"
        :pattern="inputPattern"
        v-model="value"
        :class="[errorClasses, inputClasses]"
        :placeholder="placeholder"
        readonly
      />

      <p
        v-if="hasError"
        class="my-2 text-danger"
      >
        {{ firstError }}
      </p>
    </template>
  </r64-default-field>
</template>

<script>
import { FormField, HandlesValidationErrors } from 'laravel-nova'
import R64Field from '../../mixins/R64Field'

export default {
  mixins: [HandlesValidationErrors, FormField, R64Field],

  props: {
    rowValues: {
      type: Array,
      default: () => []
    }
  },

  computed: {
    /**
     * Get the input type.
     */
    inputType() {
      return this.field.type || 'text'
    },

    /**
     * Get the input step amount.
     */
    inputStep() {
      return this.field.step
    },

    /**
     * Get the input minimum amount.
     */
    inputMin() {
      return this.field.min
    },

    /**
     * Get the input maximum amount.
     */
    inputMax() {
      return this.field.max
    },

    /**
     * Get the pattern that should be used for the field
     */
    inputPattern() {
      return this.field.pattern
    }
  },

  watch: {
    rowValues: {
      immediate: true,
      deep: true,
      handler() {
        this.fetchComputedValue()
      }
    }
  },

  methods: {
    fetchComputedValue() {
      Nova.request()
        .post(
          `/nova-r64-api/${this.resourceName}/computed/${this.field.attribute}`,
          {
            values: this.rowValues
          }
        )
        .then(({ data }) => {
          this.value = data
        })
    }
  }
}
</script>
