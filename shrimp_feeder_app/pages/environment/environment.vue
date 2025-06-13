<template>
  <view class="env-monitoring-page page-container"> <!-- Added page-container -->
    <!-- Sensor Readings Section -->
    <view class="section-title-bar">实时数据</view>
    <view class="sensor-grid">
      <view :class="['sensor-card', getStatusClass(currentReadings.dissolvedOxygen.status)]">
        <view class="sensor-name">溶氧量</view>
        <view class="sensor-value">{{ currentReadings.dissolvedOxygen.value }} <text class="sensor-unit">{{ currentReadings.dissolvedOxygen.unit }}</text></view>
        <view class="sensor-status-text">{{ getStatusText(currentReadings.dissolvedOxygen.status) }}</view>
        <view class="graph-placeholder">[溶氧量趋势图]</view>
      </view>

      <view :class="['sensor-card', getStatusClass(currentReadings.ph.status)]">
        <view class="sensor-name">pH值</view>
        <view class="sensor-value">{{ currentReadings.ph.value }} <text class="sensor-unit">{{ currentReadings.ph.unit }}</text></view>
        <view class="sensor-status-text">{{ getStatusText(currentReadings.ph.status) }}</view>
        <view class="graph-placeholder">[pH值趋势图]</view>
      </view>

      <view :class="['sensor-card', getStatusClass(currentReadings.waterTemperature.status)]">
        <view class="sensor-name">水温</view>
        <view class="sensor-value">{{ currentReadings.waterTemperature.value }} <text class="sensor-unit">{{ currentReadings.waterTemperature.unit }}</text></view>
        <view class="sensor-status-text">{{ getStatusText(currentReadings.waterTemperature.status) }}</view>
        <view class="graph-placeholder">[水温趋势图]</view>
      </view>

      <view :class="['sensor-card', getStatusClass(currentReadings.airHumidity.status)]">
        <view class="sensor-name">空气湿度</view>
        <view class="sensor-value">{{ currentReadings.airHumidity.value }} <text class="sensor-unit">{{ currentReadings.airHumidity.unit }}</text></view>
        <view class="sensor-status-text">{{ getStatusText(currentReadings.airHumidity.status) }}</view>
        <view class="graph-placeholder">[空气湿度趋势图]</view>
      </view>
    </view>

    <!-- Historical Data Section -->
    <view class="section-title-bar">历史数据查询</view>
    <view class="historical-data-form card-item"> <!-- Changed to card-item -->
      <view class="form-group">
        <label class="form-label">开始日期:</label>
        <picker mode="date" :value="startDate" :end="endDate || today" @change="bindStartDateChange">
          <view class="picker-display form-input-style">{{ startDate || '请选择' }}</view> <!-- Standardized picker display -->
        </picker>
      </view>
      <view class="form-group">
        <label class="form-label">结束日期:</label>
        <picker mode="date" :value="endDate" :start="startDate || '1970-01-01'" :end="today" @change="bindEndDateChange">
          <view class="picker-display form-input-style">{{ endDate || '请选择' }}</view> <!-- Standardized picker display -->
        </picker>
      </view>
      <button class="button query-button" @tap="queryHistoricalData">查询</button>
    </view>
  </view>
</template>

<script>
// Script content remains the same
export default {
  data() {
    const today = new Date().toISOString().slice(0, 10);
    return {
      currentReadings: {
        dissolvedOxygen: { value: '6.5', unit: 'mg/L', status: 'normal' },
        ph: { value: '7.8', unit: '', status: 'normal' },
        waterTemperature: { value: '28', unit: '°C', status: 'high' },
        airHumidity: { value: '75', unit: '%', status: 'normal' }
      },
      startDate: today,
      endDate: today,
      today: today,
    };
  },
  methods: {
    getStatusClass(status) {
      if (status === 'low') return 'status-low';
      if (status === 'high') return 'status-high';
      return 'status-normal';
    },
    getStatusText(status) {
        const statusMap = { normal: '正常', low: '偏低', high: '偏高', };
        return statusMap[status] || '未知';
    },
    bindStartDateChange(e) { this.startDate = e.detail.value; },
    bindEndDateChange(e) { this.endDate = e.detail.value; },
    queryHistoricalData() {
      if (!this.startDate || !this.endDate) {
        uni.showToast({ title: '请选择开始和结束日期', icon: 'none' });
        return;
      }
      uni.showToast({
        title: `查询历史: ${this.startDate} 至 ${this.endDate}`,
        icon: 'none',
        duration: 2000
      });
    }
  }
};
</script>

<style lang="scss">
// Conceptual Palette
// $uni-color-primary: #007AFF;
// $uni-color-success: #3cc51f;
// $uni-color-warning: #f39c12;
// $uni-color-error: #e54d42;
// $uni-text-color: #333333;
// $uni-text-color-grey: #666666;
// $uni-text-color-placeholder: #999999;
// $uni-bg-color-page: #f4f4f4;
// $uni-bg-color-white: #ffffff;
// $uni-border-color: #e0e0e0;

// $uni-spacing-base: 20rpx;
// $uni-spacing-lg: 30rpx;
// $uni-card-border-radius: 16rpx;
// $uni-card-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
// $uni-button-height: 90rpx;
// $uni-button-font-size: 32rpx;
// $uni-button-border-radius: 10rpx;
// $uni-input-height: 80rpx;
// $uni-input-border-radius: 8rpx;
// $uni-input-padding-h: 20rpx;
// $uni-font-size-title: 32rpx; // For section titles

.page-container {
  padding: 20rpx; // $uni-spacing-base;
  // background-color: #f4f4f4; // $uni-bg-color-page; // Set globally
  min-height: 100vh;
  box-sizing: border-box;
}

.section-title-bar {
  font-size: 32rpx; // $uni-font-size-title;
  font-weight: bold;
  color: #333333; // $uni-text-color;
  margin-top: 20rpx; // $uni-spacing-base; (remove top margin for first title)
  margin-bottom: 20rpx; // $uni-spacing-base;
  padding-left: 10rpx;
}
.env-monitoring-page > .section-title-bar:first-of-type {
    margin-top: 0; /* Remove top margin for the very first section title */
}


.sensor-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20rpx; // $uni-spacing-base;
}

.sensor-card {
  background-color: #ffffff; // $uni-bg-color-white;
  border-radius: 16rpx; // $uni-card-border-radius;
  padding: 20rpx; // $uni-spacing-base;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
  width: calc(50% - 10rpx);
  box-sizing: border-box;
  margin-bottom: 20rpx; // $uni-spacing-base;
  border-left-width: 10rpx;
  border-left-style: solid;
}

.sensor-card.status-normal { border-left-color: #3cc51f; } // $uni-color-success;
.sensor-card.status-low { border-left-color: #f39c12; } // $uni-color-warning;
.sensor-card.status-high { border-left-color: #e54d42; } // $uni-color-error;

.sensor-name {
  font-size: 30rpx; // $uni-font-size-lg;
  font-weight: bold;
  color: #333333; // $uni-text-color;
  margin-bottom: 10rpx;
}

.sensor-value {
  font-size: 38rpx; /* Slightly reduced for better fit */
  font-weight: bold;
  color: #333333; // $uni-text-color;
  margin-bottom: 8rpx;
}
.sensor-unit {
    font-size: 24rpx; // $uni-font-size-sm;
    color: #666666; // $uni-text-color-grey;
    margin-left: 5rpx;
}
.sensor-status-text {
    font-size: 26rpx;
    color: #666666; // $uni-text-color-grey;
    margin-bottom: 15rpx;
}

.graph-placeholder {
  font-size: 26rpx;
  color: #999999; // $uni-text-color-placeholder;
  text-align: center;
  padding: 30rpx 10rpx;
  border: 1rpx dashed #cccccc; // Lighter dash
  border-radius: 8rpx; // $uni-input-border-radius;
  margin-top: 15rpx; /* Added margin */
  background-color: #f9f9f9;
}

/* Historical Data Form */
.card-item { // Common card style for the form section
  background-color: #ffffff; // $uni-bg-color-white;
  border-radius: 16rpx; // $uni-card-border-radius;
  padding: 30rpx; // $uni-spacing-lg;
  margin-bottom: 20rpx; // $uni-spacing-base; // For consistency if other cards follow
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
}

.form-group {
  display: flex;
  align-items: center;
  margin-bottom: 25rpx;
}
.form-group:last-of-type { /* Remove margin for last group before button */
    margin-bottom: 0;
}


.form-label {
  font-size: 30rpx; // $uni-font-size-lg;
  color: #333333; // $uni-text-color;
  width: 160rpx; /* Adjusted width */
  margin-right: 20rpx; // $uni-spacing-base;
  flex-shrink: 0;
}

.form-input-style { // Common style for inputs / picker displays
  flex-grow: 1;
  height: 80rpx; // $uni-input-height;
  line-height: 80rpx;
  border: 1rpx solid #e0e0e0; // $uni-border-color;
  border-radius: 8rpx; // $uni-input-border-radius;
  padding: 0 20rpx; // $uni-input-padding-h;
  font-size: 28rpx; // $uni-font-size-base;
  background-color: #ffffff; // $uni-bg-color-white;
  color: #555555;
  text-align: left; /* Align picker text to left for better readability */
}
.picker-display { // Applied to the view within picker
   @extend .form-input-style;
   text-align: left; // Ensure picker text is also left-aligned
}
input::placeholder, .picker-display:empty::before { /* Placeholder style for picker */
    // content: attr(placeholder); /* This doesn't work well in all uni-app scenarios for view */
    color: #999999; // $uni-text-color-placeholder;
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

.query-button {
  @extend .button;
  width: 100%;
  background-color: #007AFF; // $uni-color-primary;
  color: white;
  font-size: 32rpx; // $uni-button-font-size;
  border-radius: 10rpx; // $uni-button-border-radius;
  height: 90rpx; // $uni-button-height;
  line-height: 90rpx;
  margin-top: 30rpx; // $uni-spacing-lg; (Spacing from last form group to button)
}
</style>
