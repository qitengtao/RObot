<template>
  <view class="profile-page-container">
    <view class="user-info-card card">
      <view class="avatar-placeholder">
        <text class="avatar-text">头像</text>
      </view>
      <text class="nickname-text">{{ nickname }}</text>
      <view class="info-item static-info">
        <text class="info-label">User ID:</text>
        <text class="info-value">{{ userId }}</text>
      </view>
    </view>

    <view class="settings-card card">
      <view class="section-title">通知设置</view>
      <view class="setting-item">
        <text class="setting-label">投喂前提醒</text>
        <switch :checked="preFeedingReminderEnabled" @change="togglePreFeedingReminder" color="#3cc51f" style="transform: scale(0.9)"/>
      </view>
      <view class="setting-item">
        <text class="setting-label">异常报警推送</text>
        <switch :checked="abnormalAlertsEnabled" @change="toggleAbnormalAlerts" color="#3cc51f" style="transform: scale(0.9)"/>
      </view>
    </view>

    <button class="button logout-button" @tap="handleLogout">退出登录</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      nickname: '微信用户',
      userId: 'user_placeholder_12345',
      preFeedingReminderEnabled: true,
      abnormalAlertsEnabled: true,
    }
  },
  onLoad() {
    // Lifecycle hook
  },
  methods: {
    handleLogout() {
      console.log('Logout button tapped');
      uni.showToast({
        title: '已退出登录',
        icon: 'none',
        duration: 1500
      });
      setTimeout(() => {
        uni.reLaunch({
          url: '/pages/login/login'
        });
      }, 1500);
    },
    togglePreFeedingReminder(e) {
      this.preFeedingReminderEnabled = e.detail.value;
      uni.showToast({
        title: `投喂前提醒已${this.preFeedingReminderEnabled ? '开启' : '关闭'}`,
        icon: 'none'
      });
    },
    toggleAbnormalAlerts(e) {
      this.abnormalAlertsEnabled = e.detail.value;
      uni.showToast({
        title: `异常报警推送已${this.abnormalAlertsEnabled ? '开启' : '关闭'}`,
        icon: 'none'
      });
    }
  }
}
</script>

<style lang="scss">
// Conceptual Palette (not using SCSS vars due to tool limits)
// $uni-color-danger: #ff4d4f;
// $uni-color-success: #3cc51f;
// $uni-text-color: #333333;
// $uni-text-color-grey: #666666;
// $uni-bg-color-page: #f4f4f4;
// $uni-bg-color-white: #ffffff;
// $uni-border-color-light: #eeeeee;
// $uni-border-color-extralight: #f5f5f5;

// $uni-spacing-lg: 30rpx;
// $uni-spacing-base: 20rpx;
// $uni-card-border-radius: 16rpx;
// $uni-card-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
// $uni-button-height: 90rpx;
// $uni-button-font-size: 32rpx;
// $uni-button-border-radius: 10rpx; // For main action buttons

.profile-page-container {
  padding: 30rpx; // $uni-spacing-lg;
  // background-color: #f4f4f4; // $uni-bg-color-page; // Set globally by page {}
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
}

.card {
  background-color: #ffffff; // $uni-bg-color-white;
  border-radius: 16rpx; // $uni-card-border-radius;
  padding: 30rpx; // $uni-card-padding;
  margin-bottom: 30rpx; // $uni-spacing-lg;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
}

.user-info-card {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.avatar-placeholder {
  width: 160rpx;
  height: 160rpx;
  border-radius: 50%;
  background-color: #e0e0e0; // A light grey, good for placeholders
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20rpx; // $uni-spacing-base;
}

.avatar-text {
  color: #999999; // $uni-text-color-placeholder;
  font-size: 30rpx; // $uni-font-size-lg;
}

.nickname-text {
  font-size: 36rpx; // $uni-font-size-page-title or slightly smaller
  font-weight: bold;
  color: #333333; // $uni-text-color;
  margin-bottom: 30rpx; // $uni-spacing-lg;
}

.info-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding: 20rpx 0; // $uni-spacing-base
  font-size: 28rpx; // $uni-font-size-base;
}
.static-info {
   border-top: 1rpx solid #eeeeee; // $uni-border-color-light;
   margin-top: 20rpx; // $uni-spacing-base;
}

.info-label {
  color: #666666; // $uni-text-color-grey;
}

.info-value {
  color: #333333; // $uni-text-color;
}

.settings-card {} // No specific styles beyond .card needed currently

.section-title {
  font-size: 32rpx; // $uni-font-size-title;
  font-weight: bold;
  color: #333333; // $uni-text-color;
  margin-bottom: 20rpx; // $uni-spacing-base;
  padding-bottom: 20rpx; // $uni-spacing-base;
  border-bottom: 1rpx solid #eeeeee; // $uni-border-color-light;
}

.setting-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20rpx 0; // $uni-spacing-base
  font-size: 30rpx; // $uni-font-size-lg;
  border-bottom: 1rpx solid #f5f5f5; // $uni-border-color-extralight or similar
}
.setting-item:last-child {
  border-bottom: none;
}

.setting-label {
  color: #333333; // $uni-text-color;
}

/* General button class */
.button {
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  &:active { // Basic active state
    opacity: 0.8;
  }
}

.logout-button {
  width: 100%;
  height: 90rpx; // $uni-button-height;
  background-color: #ff4d4f; // $uni-color-danger;
  color: white;
  font-size: 32rpx; // $uni-button-font-size;
  border-radius: 10rpx; // $uni-button-border-radius;
  margin-top: 30rpx; // $uni-spacing-lg, increased for more separation
  border: none;
}
</style>
