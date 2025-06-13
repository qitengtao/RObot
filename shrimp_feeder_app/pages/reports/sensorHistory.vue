<template>
  <view class="sensor-history-page page-container"> <!-- Added page-container -->
    <view class="filter-section card-item"> <!-- Used card-item -->
      <view class="form-group">
        <label class="form-label">传感器:</label>
        <picker :value="sensorIndex" :range="sensorOptions" range-key="text" @change="bindSensorChange">
          <view class="picker-display form-input-style">{{ sensorOptions[sensorIndex].text }}</view>
        </picker>
      </view>
      <view class="form-group">
        <label class="form-label">开始日期:</label>
        <picker mode="date" :value="startDate" :end="endDate || today" @change="bindStartDateChange">
          <view class="picker-display form-input-style">{{ startDate || '请选择日期' }}</view>
        </picker>
      </view>
      <view class="form-group">
        <label class="form-label">结束日期:</label>
        <picker mode="date" :value="endDate" :start="startDate || '1970-01-01'" :end="today" @change="bindEndDateChange">
          <view class="picker-display form-input-style">{{ endDate || '请选择日期' }}</view>
        </picker>
      </view>
      <button class="button query-button" @tap="queryHistory">查询历史</button>
    </view>

    <view class="chart-section card-item"> <!-- Used card-item -->
      <view class="section-title-bar chart-main-title">{{ sensorOptions[sensorIndex].text }} - 历史趋势</view> <!-- Standardized title -->
      <view class="chart-placeholder">
        <text>[{{ sensorOptions[sensorIndex].text }} 历史趋势图区域]</text>
      </view>
       <view class="chart-placeholder" style="margin-top: 20rpx;"> <!-- $uni-spacing-base -->
        <text>[{{ sensorOptions[sensorIndex].text }} 历史数据表格区域]</text>
      </view>
    </view>
  </view>
</template>

<script>
// Script content remains the same
export default {
  data() {
    const today = new Date().toISOString().slice(0, 10);
    return {
      today: today,
      startDate: today,
      endDate: today,
      sensorOptions: [
        { text: '溶氧量', value: 'dissolvedOxygen' },
        { text: 'pH值', value: 'ph' },
        { text: '水温', value: 'waterTemperature' },
        { text: '空气湿度', value: 'airHumidity' }
      ],
      sensorIndex: 0,
    };
  },
  methods: {
    bindSensorChange(e) { this.sensorIndex = e.detail.value; },
    bindStartDateChange(e) { this.startDate = e.detail.value; },
    bindEndDateChange(e) { this.endDate = e.detail.value; },
    queryHistory() {
      if (!this.startDate || !this.endDate) {
        uni.showToast({ title: '请选择开始和结束日期', icon: 'none' });
        return;
      }
      const selectedSensor = this.sensorOptions[this.sensorIndex].text;
      uni.showToast({
        title: `查询 ${selectedSensor} 从 ${this.startDate} 至 ${this.endDate}`,
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
// $uni-button-height: 80rpx; // Standard button/input height
// $uni-button-font-size: 30rpx;
// $uni-input-height: 80rpx;
// $uni-input-border-radius: 8rpx;
// $uni-input-padding-h: 20rpx;
// $uni-font-size-title: 32rpx;

.page-container {
  padding: 20rpx; // $uni-spacing-base;
  // background-color: #f4f4f4; // Set globally
  min-height: 100vh;
  box-sizing: border-box;
}

.card-item {
  background-color: #ffffff; // $uni-bg-color-white;
  border-radius: 16rpx; // $uni-card-border-radius;
  padding: 25rpx;
  margin-bottom: 20rpx; // $uni-spacing-base;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
}

.filter-section {
  // Uses card-item
}

.form-group {
  display: flex;
  align-items: center;
  margin-bottom: 20rpx; // $uni-spacing-base;
}
.form-group:last-of-type {
    margin-bottom: 0; /* Remove margin for last group before button */
}


.form-label {
  font-size: 30rpx; // $uni-font-size-lg;
  color: #333333; // $uni-text-color;
  width: 140rpx; /* Adjusted width */
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
  text-align: left; /* Align picker text to left */
}
.picker-display { // Applied to the view within picker
   @extend .form-input-style;
}
input::placeholder, .picker-display:empty::before {
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
  font-size: 30rpx; // $uni-button-font-size;
  border-radius: 8rpx; // $uni-input-border-radius; (match inputs)
  height: 80rpx; // $uni-button-height; (match inputs)
  line-height: 80rpx;
  margin-top: 30rpx; // $uni-spacing-lg;
}

.chart-section {
  // Uses card-item
}

.section-title-bar { // Standard section title
  font-size: 32rpx; // $uni-font-size-title;
  font-weight: bold;
  color: #333333; // $uni-text-color;
  margin-bottom: 20rpx; // $uni-spacing-base;
  // padding-bottom: 15rpx; // Optional: if border is used
  // border-bottom: 1rpx solid #eeeeee; // Optional: if border is used
}
.chart-main-title { // Specific for the chart title
    @extend .section-title-bar;
    text-align: center;
    padding-bottom: 0; /* Remove padding if no border */
    border-bottom: none; /* Remove border if not desired for this title */
}


.chart-placeholder {
  font-size: 28rpx; // $uni-font-size-base;
  color: #999999; // $uni-text-color-placeholder;
  text-align: center;
  padding: 60rpx 20rpx; /* Adjusted padding */
  border: 1rpx dashed #cccccc;
  border-radius: 8rpx; // $uni-input-border-radius;
  background-color: #f9f9f9;
}
</style>
