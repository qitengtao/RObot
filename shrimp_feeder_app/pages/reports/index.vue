<template>
  <view class="reports-index-page page-container"> <!-- Added page-container -->
    <view class="list-navigation-item card-item" @tap="navigateToReport('/pages/reports/feedingLog')">
      <uni-icons type="calendar" size="24" color="#007AFF" class="item-icon"></uni-icons>
      <text class="item-text">投喂日志</text>
      <uni-icons type="forward" size="22" color="#B0B0B0" class="item-arrow"></uni-icons>
    </view>
    <view class="list-navigation-item card-item" @tap="navigateToReport('/pages/reports/sensorHistory')">
      <uni-icons type="line-chart" size="24" color="#007AFF" class="item-icon"></uni-icons>
      <text class="item-text">传感器历史</text>
      <uni-icons type="forward" size="22" color="#B0B0B0" class="item-arrow"></uni-icons>
    </view>
    <view class="list-navigation-item card-item" @tap="navigateToReport('/pages/reports/abnormalEvents')">
      <uni-icons type="notification" size="24" color="#007AFF" class="item-icon"></uni-icons>
      <text class="item-text">异常事件记录</text>
      <uni-icons type="forward" size="22" color="#B0B0B0" class="item-arrow"></uni-icons>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {
    navigateToReport(url) {
      uni.navigateTo({
        url: url
      });
    }
  }
};
</script>

<style lang="scss">
// Conceptual Palette
// $uni-color-primary: #007AFF;
// $uni-text-color: #333333;
// $uni-text-color-lightgrey: #B0B0B0; // For arrow icon
// $uni-bg-color-page: #f4f4f4;
// $uni-bg-color-white: #ffffff;
// $uni-spacing-base: 20rpx;
// $uni-spacing-lg: 30rpx;
// $uni-card-border-radius: 16rpx;
// $uni-card-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);

.page-container {
  padding: 20rpx; // $uni-spacing-base;
  // background-color: #f4f4f4; // $uni-bg-color-page; // Set globally
  min-height: 100vh;
  box-sizing: border-box;
}

.card-item { // Common card style, can also be specific if needed
  background-color: #ffffff; // $uni-bg-color-white;
  border-radius: 16rpx; // $uni-card-border-radius;
  // padding: 30rpx; // $uni-spacing-lg; // Padding defined in list-navigation-item
  margin-bottom: 20rpx; // $uni-spacing-base;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
}

.list-navigation-item { // Specific for these navigation links
  display: flex;
  align-items: center;
  padding: 30rpx; // $uni-spacing-lg;
  transition: background-color 0.15s ease-in-out; // Smoother transition

  &:active {
    background-color: #f0f0f0; // Darken background on tap
  }
}

.item-icon {
  margin-right: 25rpx; /* Slightly more spacing */
  flex-shrink: 0; /* Prevent icon from shrinking */
}

.item-text {
  flex-grow: 1;
  font-size: 32rpx;
  color: #333333; // $uni-text-color;
  font-weight: 500; /* Slightly bolder text for navigation items */
}

.item-arrow {
    margin-left: 10rpx; /* Space before arrow */
    flex-shrink: 0;
}
</style>
