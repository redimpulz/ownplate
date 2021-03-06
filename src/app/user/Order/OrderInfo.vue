<template>
  <div class="bg-surface r-8 d-low m-t-8 p-b-24">
    <!-- Order Items -->
    <template v-for="orderItem in orderItems">
      <order-item :key="orderItem.key" :order-item="orderItem"></order-item>
    </template>

    <hr class="devider m-t-16 m-b-0 m-l-16 m-r-16" />

    <!-- Totals -->
    <div v-if="verified">
      <!-- Sub Total -->
      <div class="p-t-16 p-l-16 p-r-16">
        <div class="cols">
          <div class="flex-1">
            <div class="t-body1 c-text-black-high">
              {{ $t("order.subtotal") }}
            </div>
          </div>
          <div class="align-righ">
            <span class="t-body1 c-text-black-high">
              {{ $n(orderInfo.sub_total, "currency") }}
            </span>
          </div>
        </div>
      </div>

      <!-- Tax -->
      <div class="p-t-8 p-l-16 p-r-16">
        <div class="cols">
          <div class="flex-1">
            <div class="t-body1 c-text-black-high">
              {{
                $t(
                  orderInfo.inclusiveTax
                    ? "order.inclusiveTax"
                    : "order.salesTax"
                )
              }}
            </div>
          </div>
          <div class="align-righ">
            <span class="t-body1 c-text-black-high">
              {{ $n(orderInfo.tax, "currency") }}
            </span>
          </div>
        </div>
      </div>

      <!-- Total -->
      <div
        v-if="false && regionTip.choices.length > 0"
        class="p-t-8 p-l-16 p-r-16"
      >
        <div class="cols">
          <div class="flex-1">
            <div class="t-body1 c-text-black-high">
              {{ $t("order.total") }}
            </div>
          </div>
          <div class="align-righ">
            <span class="t-body1 c-text-black-high">
              {{ $n(orderInfo.total, "currency") }}
            </span>
          </div>
        </div>
      </div>

      <hr
        v-if="regionTip.choices.length > 0 && isTipEditable"
        class="devider m-t-16 m-b-0 m-l-16 m-r-16"
      />

      <!-- Tip -->
      <div
        v-if="regionTip.choices.length > 0 && (isTipEditable || tip > 0)"
        class="p-t-16 p-l-16 p-r-16"
      >
        <div class="cols">
          <div class="flex-1">
            <div class="t-body1 c-text-black-high">
              {{ $t("order.tip") }}
            </div>
          </div>
          <div class="align-righ">
            <span class="t-body1 c-text-black-high">
              {{ $n(tip, "currency") }}
            </span>
          </div>
        </div>
      </div>

      <!-- Tip Buttons -->
      <div v-if="regionTip.choices.length > 0" class="p-t-16 p-l-16 p-r-16">
        <div v-if="isTipEditable">
          <div>
            <b-input
              v-model="tip"
              class="w-full"
              type="number"
              :placeholder="$t('order.maxTip', { max: regionTip.max })"
              :step="tipStep"
              maxlength="30"
              style
              @input="handleTipInput"
            />
          </div>
          <div>
            <b-button
              v-for="ratio in regionTip.choices"
              :key="ratio"
              class="m-t-8 m-r-8 bg-form"
              :type="isSameAmount(ratio) ? 'is-primary' : ''"
              @click="updateTip(ratio)"
            >
              {{ ratio + "%" }}
            </b-button>
          </div>
        </div>
      </div>

      <hr class="devider m-t-16 m-b-0 m-l-16 m-r-16" />

      <!-- Total Charge -->
      <div class="p-t-24 p-l-16 p-r-16">
        <div class="cols">
          <div class="flex-1">
            <div class="t-h6 c-status-green">
              {{ $t("order.totalCharge") }}
            </div>
          </div>
          <div class="align-righ">
            <span class="t-h6 c-status-green">
              {{ $n(orderInfo.total + Number(tip), "currency") }}
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { order_status } from "~/plugins/constant.js";
import OrderItem from "~/app/user/Order/OrderItem";

export default {
  name: "Order",
  components: {
    OrderItem
  },

  props: {
    orderItems: {
      type: Array,
      required: true
    },
    orderInfo: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      tip: ""
    };
  },
  computed: {
    regionTip() {
      return this.$store.getters.stripeRegion.tip;
    },
    tipStep() {
      return 1.0 / this.$store.getters.stripeRegion.multiple;
    },
    verified() {
      return this.orderInfo.status >= order_status.validation_ok;
    },
    isTipEditable() {
      return this.orderInfo.status === order_status.validation_ok;
    },
    maxTip() {
      return this.calcTip(this.regionTip.max);
    }
  },
  watch: {
    orderInfo() {
      console.log("orderInfo changed", this.orderInfo.total);
      if (this.isTipEditable) {
        if (this.tip === "" && this.regionTip.default === 0) {
          return; // display the placeholder
        }
        this.updateTip(this.regionTip.default);
      } else {
        this.tip = this.orderInfo.tip;
      }
    }
  },
  methods: {
    calcTip(ratio) {
      const m = this.$store.getters.stripeRegion.multiple;
      const value = Math.round((this.orderInfo.total * ratio * m) / 100) / m;
      if (m === 1) {
        return Math.round(value);
      }
      return Math.round(value * m) / m;
    },
    updateTip(ratio) {
      this.tip = this.calcTip(ratio);
      this.$emit("change", this.tip);
    },
    isSameAmount(ratio) {
      return Number(this.tip) === this.calcTip(ratio);
    },
    handleTipInput() {
      if (this.tip < 0) {
        console.log("negative");
        this.tip = -this.tip;
      } else if (this.tip > this.maxTip) {
        console.log("max");
        this.tip = this.maxTip;
      }
      this.$emit("change", Number(this.tip));
    }
  }
};
</script>
