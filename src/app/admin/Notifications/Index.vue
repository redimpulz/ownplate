<template>
  <div>
    <!-- Notification Settings Button -->
    <notification-setting-button
      :notification-data="notificationData || defaultNotificationData"
      @openNotificationSettings="openNotificationSettings"
    />

    <!-- Notification Settings Popup-->
    <notification-settings
      v-if="notificationData"
      :shop-info="shopInfo"
      :notification-data="notificationData"
      :notification-settings-popup="NotificationSettingsPopup"
      @close="closeNotificationSettings"
    />
  </div>
</template>

<script>
import { db, firestore } from "~/plugins/firebase.js";

import NotificationSettings from "./NotificationSettings";
import NotificationSettingButton from "./NotificationSettingButton";

export default {
  components: {
    NotificationSettings,
    NotificationSettingButton
  },
  props: {
    shopInfo: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      NotificationSettingsPopup: false,
      notificationData: null,
      defaultNotificationData: {
        soundOn: null,
        infinityNotification: null,
        uid: this.$store.getters.uidAdmin,
        createdAt: firestore.FieldValue.serverTimestamp()
      }
    };
  },
  async created() {
    try {
      const notification = await db
        .doc(`restaurants/${this.restaurantId()}/private/notifications`)
        .get();
      this.notificationData = notification.exists
        ? Object.assign(this.defaultNotificationData, notification.data())
        : this.defaultNotificationData;
    } catch (error) {
      if (error.code === "permission-denied") {
        // We can ignore this type of error here
        console.warn("Ignoring", error.code);
      } else {
        throw error;
      }
    }
  },
  methods: {
    openNotificationSettings() {
      this.NotificationSettingsPopup = true;
    },
    closeNotificationSettings() {
      this.NotificationSettingsPopup = false;
    }
  }
};
</script>
