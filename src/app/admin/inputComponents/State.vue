<template>
  <div>
    <div class="t-subtitle2 c-text-black-medium p-b-8">
      {{ $t(this.state_key) }}
      <span class="c-status-red">*</span>
    </div>
    <b-field
      v-if="Array.isArray(states)"
      :type="errors['state'].length > 0 ? 'is-danger' : 'is-success'"
    >
      <b-select :value="value" placeholder="select" @input="input">
        <option v-for="stateItem in states" :key="stateItem">
          {{ stateItem }}
        </option>
      </b-select>
    </b-field>
    <b-field
      v-else
      :type="errors['state'].length > 0 ? 'is-danger' : 'is-success'"
    >
      <b-input
        :value="value"
        type="text"
        :placeholder="$t('editRestaurant.enterCity')"
        maxlength="15"
      ></b-input>
    </b-field>
  </div>
</template>

<script>
export default {
  name: "State",
  props: {
    errors: {
      type: Object,
      required: true
    },
    value: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      states: [],
      state_key: ""
    };
  },
  created() {
    this.states = this.regionalSetting.AddressStates;
    this.state_key = this.regionalSetting.StateKey || "shopInfo.state";
  },
  methods: {
    input(e) {
      this.$emit("input", e);
    }
  }
};
</script>
