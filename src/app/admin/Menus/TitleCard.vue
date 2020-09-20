<template>
  <div>
    <!-- Title Card -->
    <div
      class="bg-form r-8 p-l-16 p-r-16 p-t-16 p-b-16 m-t-24"
      @click="toEdit()"
    >
      <div v-if="title.name == ''" class="t-h6 c-text-black-disabled" if>
        {{ $t("editTitle.empty") }}
      </div>
      <div v-else class="t-h6 c-text-black-disabled" if>
        {{ title.name }}
      </div>
    </div>

    <!-- Card Actions -->
    <div class="m-t-8 p-b-8 p-l-8 p-r-8">
      <div class="cols">
        <div class="flex-1">
          <!-- Position Up -->
          <b-button
            v-if="position !== 'first'"
            class="b-reset op-button-pill h-36 bg-primary-bg m-r-8"
            @click="positionUp"
          >
            <i class="material-icons c-primary s-18 p-l-8 p-r-8">
              >arrow_upward
            </i>
          </b-button>
          <!-- Disable if First -->
          <b-button
            v-else
            class="b-reset op-button-pill h-36 bg-primary-bg m-r-8"
            disabled
          >
            <i class="material-icons c-text-black-disabled s-18 p-l-8 p-r-8">
              >arrow_upward
            </i>
          </b-button>

          <!-- Position Down -->
          <b-button
            v-if="position !== 'last'"
            class="b-reset op-button-pill h-36 bg-primary-bg m-r-8"
            @click="positionDown"
          >
            <i class="material-icons c-primary s-18 p-l-8 p-r-8">
              >arrow_downward
            </i>
          </b-button>
          <!-- Disable if Last -->
          <b-button
            v-else
            class="b-reset op-button-pill h-36 bg-primary-bg m-r-8"
            disabled
          >
            <i class="material-icons c-text-black-disabled s-18 p-l-8 p-r-8">
              >arrow_downward
            </i>
          </b-button>

          <!-- Duplicate -->
          <b-button
            class="b-reset op-button-pill h-36 bg-primary-bg m-r-8"
            @click="forkItem"
          >
            <i class="material-icons c-primary s-18 p-l-8 p-r-8">queue</i>
          </b-button>
        </div>
        <div>
          <b-button
            class="b-reset op-button-pill h-36 bg-status-red-bg"
            @click="deleteItem"
          >
            <i class="material-icons c-status-red s-18 p-l-8 p-r-8">delete</i>
          </b-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import store from "~/store/index.js";

export default {
  props: {
    title: {
      type: Object,
      required: true
    },
    position: {
      type: String,
      required: true
    }
  },
  data() {
    return {};
  },
  methods: {
    toEdit() {
      this.$emit("toEditMode", this.title.id);
    },
    positionUp() {
      this.$emit("positionUp", this.title.id);
    },
    positionDown() {
      this.$emit("positionDown", this.title.id);
    },
    forkItem() {
      this.$emit("forkItem", this.title.id);
    },
    deleteItem() {
      // this.$emit("deleteItem", this.title.id);
      this.$store.commit("setAlert", {
        code: "editMenu.reallyDelete",
        callback: () => {
          this.$emit("deleteItem", this.title.id);
        }
      });
    }
  }
};
</script>
