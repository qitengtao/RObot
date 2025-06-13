<template>
  <view class="strategies-page-container page-container"> <!-- Added page-container -->
    <view v-if="strategies.length === 0 && !isLoading" class="empty-state">
      <text>暂无策略，请点击下方按钮添加</text>
    </view>

    <view v-for="(strategy, index) in strategies" :key="strategy.id" class="strategy-card card-item">
      <view class="strategy-header">
        <text class="strategy-name">{{ strategy.name }}</text>
        <switch :checked="strategy.isEnabled" @change="toggleStrategy(index)" color="#3cc51f" style="transform:scale(0.85)" />
      </view>
      <view class="strategy-details">
        <text class="detail-item"><text class="detail-label">时间:</text> {{ strategy.time }}</text>
        <text class="detail-item"><text class="detail-label">投喂量:</text> {{ strategy.amount }}{{ strategy.unit }}</text>
        <text class="detail-item"><text class="detail-label">周期:</text> {{ strategy.cycleText }}</text>
        <text class="detail-item" v-if="strategy.cycleValue === 'specific' && strategy.specificDate"><text class="detail-label">特定日期:</text> {{ strategy.specificDate }}</text>
      </view>
      <view class="strategy-actions">
        <button class="button action-button edit-button" @tap="editStrategy(strategy.id)">编辑</button>
      </view>
    </view>

    <button class="button fab-add-strategy" @tap="addNewStrategy"> <!-- Renamed class for clarity -->
      <uni-icons type="plusempty" size="22" color="#fff" style="margin-right: 8rpx;"></uni-icons>
      新增策略
    </button>
  </view>
</template>

<script>
// Script content remains the same
export default {
  data() {
    return {
      isLoading: true,
      strategies: []
    }
  },
  onShow() {
    this.loadStrategies();
  },
  methods: {
    loadStrategies() {
      this.isLoading = true;
      if (typeof getApp !== 'undefined' && getApp().globalData.mockStrategies && getApp().globalData.mockStrategies.length > 0) {
          this.strategies = JSON.parse(JSON.stringify(getApp().globalData.mockStrategies));
      } else {
           this.strategies = [
            { id: 's1', name: '晨间主力投喂', time: '08:00', amount: '2', unit: '斤', cycleText: '每天', cycleValue: 'daily', isEnabled: true },
            { id: 's2', name: '傍晚辅助加餐', time: '17:30', amount: '1.5', unit: '斤', cycleText: '每周一,三,五', cycleValue: 'weekly', cycleDays: [1,3,5], isEnabled: true },
            { id: 's3', name: '夜间观察投喂', time: '22:00', amount: '1', unit: '斤', cycleText: '特定日期', cycleValue: 'specific', specificDate: '2023-11-15', isEnabled: false }
          ];
          if (typeof getApp !== 'undefined' && getApp().globalData) { // Check globalData exists
            getApp().globalData.mockStrategies = JSON.parse(JSON.stringify(this.strategies));
          }
      }
      this.isLoading = false;
    },
    toggleStrategy(index) {
      this.strategies[index].isEnabled = !this.strategies[index].isEnabled;
      if (typeof getApp !== 'undefined' && getApp().globalData) {
          getApp().globalData.mockStrategies = JSON.parse(JSON.stringify(this.strategies));
      }
      uni.showToast({
        title: `策略已${this.strategies[index].isEnabled ? '启用' : '停用'}`,
        icon: 'none'
      });
    },
    editStrategy(strategyId) {
      uni.navigateTo({
        url: `/pages/strategies/editStrategy?id=${strategyId}`
      });
    },
    addNewStrategy() {
      uni.navigateTo({
        url: '/pages/strategies/editStrategy'
      });
    }
  }
}
</script>

<style lang="scss">
// Conceptual Palette
// $uni-color-primary: #007AFF;
// $uni-color-success: #3cc51f;
// $uni-text-color: #333333;
// $uni-text-color-grey: #666666;
// $uni-text-color-placeholder: #999999;
// $uni-bg-color-page: #f4f4f4;
// $uni-border-color-light: #eeeeee;

// $uni-spacing-base: 20rpx;
// $uni-spacing-lg: 30rpx;
// $uni-card-border-radius: 16rpx;
// $uni-card-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
// $uni-button-small-height: 60rpx;
// $uni-button-small-font-size: 28rpx;
// $uni-button-height: 90rpx;
// $uni-button-font-size: 32rpx;


.page-container { // Standard page padding
  padding: 20rpx; // $uni-spacing-base;
  padding-bottom: 160rpx; /* Increased padding for FAB */
  box-sizing: border-box;
}

.empty-state {
  text-align: center;
  padding: 150rpx 40rpx; /* Adjusted padding */
  color: #999999; // $uni-text-color-placeholder;
  font-size: 30rpx; // $uni-font-size-lg;
}

.card-item { // Common card style
  background-color: #ffffff;
  border-radius: 16rpx; // $uni-card-border-radius;
  padding: 24rpx; // Keep slightly less than page padding
  margin-bottom: 20rpx; // $uni-spacing-base;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
}

.strategy-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20rpx; // $uni-spacing-base;
}

.strategy-name {
  font-size: 32rpx; // $uni-font-size-title;
  font-weight: bold;
  color: #333333; // $uni-text-color;
}

.strategy-details {
  font-size: 28rpx; // $uni-font-size-base;
  color: #666666; // $uni-text-color-grey;
  line-height: 1.7; /* Improved line height */
}
.detail-item { // Class for each text line in details
  display: block;
  margin-bottom: 10rpx; // $uni-spacing-sm;
}
.detail-label { // Class for the label part like "时间:"
    font-weight: 500; // Slightly bolder label
    color: #555555; // Darker grey for labels
    margin-right: 10rpx;
}

.strategy-actions {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin-top: 20rpx; // $uni-spacing-base;
  padding-top: 20rpx; // $uni-spacing-base;
  border-top: 1rpx solid #eeeeee; // $uni-border-color-light;
}

/* General button class */
.button {
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  border: none;
  &:active {
    opacity: 0.8;
  }
}

.action-button { // For buttons within cards like "Edit"
  @extend .button;
  font-size: 28rpx; // $uni-button-small-font-size;
  padding: 0 30rpx;
  height: 64rpx; // Slightly taller for better touch
  line-height: 64rpx;
  border-radius: 8rpx;
  margin-left: 20rpx; // $uni-spacing-base;
}

.edit-button {
  background-color: #007AFF; // $uni-color-primary;
  color: white;
}

.fab-add-strategy { // Renamed from add-strategy-button for clarity
  @extend .button;
  position: fixed;
  bottom: 50rpx; // Consistent FAB position
  left: 50%;
  transform: translateX(-50%);
  width: calc(100% - 80rpx);
  max-width: 500rpx; /* Slightly reduced max-width */
  height: 90rpx; // $uni-button-height;
  background-color: #3cc51f; // $uni-color-success; (Green for "add" is fine)
  color: white;
  font-size: 32rpx; // $uni-button-font-size;
  border-radius: 45rpx; /* Pill shape */
  box-shadow: 0 8rpx 20rpx rgba(0, 0, 0, 0.15);
  z-index: 10;
}
</style>
