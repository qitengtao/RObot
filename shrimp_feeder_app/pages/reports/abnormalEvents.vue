<template>
  <view class="abnormal-events-page page-container"> <!-- Added page-container -->
    <view class="filter-section card-item"> <!-- Used card-item -->
      <view class="form-group">
        <label class="form-label">事件类型:</label>
        <picker :value="eventTypeIndex" :range="eventTypeOptions" range-key="text" @change="bindEventTypeChange">
          <view class="picker-display form-input-style">{{ eventTypeOptions[eventTypeIndex].text }}</view>
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
      <button class="button query-button" @tap="queryEvents">查询事件</button>
    </view>

    <view v-if="filteredEvents.length === 0" class="empty-state card-item"> <!-- Used card-item -->
      <text>当前筛选条件下无异常事件记录</text>
    </view>

    <view v-for="event in filteredEvents" :key="event.id" class="event-item card-item"> <!-- Used card-item -->
      <view class="event-header">
        <text class="event-type">{{ event.eventType }}</text>
        <text class="event-datetime">{{ event.dateTime }}</text>
      </view>
      <view class="event-body">
        <text class="event-description">{{ event.description }}</text>
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
      eventTypeOptions: [
        { text: '全部类型', value: 'all' },
        { text: '设备离线', value: 'device_offline' },
        { text: '投喂失败', value: 'feed_failure' },
        { text: '参数超标', value: 'param_exceeded' }
      ],
      eventTypeIndex: 0,
      allEvents: [
        { id: 'ae1', dateTime: '2023-10-28 09:15', eventType: '设备离线', eventTypeValue: 'device_offline', description: '2号投料机无响应' },
        { id: 'ae3', dateTime: `${today} 08:30`, eventType: '参数超标', eventTypeValue: 'param_exceeded', description: 'pH值过高 (8.5)' },
        { id: 'ae2', dateTime: '2023-10-27 15:00', eventType: '参数超标', eventTypeValue: 'param_exceeded', description: '溶氧量低于阈值 (3.5 mg/L)' },
        { id: 'ae4', dateTime: `${new Date(Date.now() - 86400000 * 2).toISOString().slice(0,10)} 11:00`, eventType: '投喂失败', eventTypeValue: 'feed_failure', description: '1号投料机投喂指令超时' }
      ],
      filteredEvents: []
    };
  },
  created() { this.queryEvents(); },
  methods: {
    bindEventTypeChange(e) { this.eventTypeIndex = e.detail.value; },
    bindStartDateChange(e) { this.startDate = e.detail.value; },
    bindEndDateChange(e) { this.endDate = e.detail.value; },
    queryEvents() {
      uni.showLoading({ title: '查询中...' });
      const selectedEventTypeValue = this.eventTypeOptions[this.eventTypeIndex].value;
      setTimeout(() => {
        this.filteredEvents = this.allEvents.filter(event => {
          const eventDate = new Date(event.dateTime.split(' ')[0]);
          const start = new Date(this.startDate);
          const end = new Date(this.endDate);
          const typeMatch = selectedEventTypeValue === 'all' || event.eventTypeValue === selectedEventTypeValue;
          return typeMatch && eventDate >= start && eventDate <= end;
        }).sort((a, b) => new Date(b.dateTime) - new Date(a.dateTime));
        uni.hideLoading();
        if(this.filteredEvents.length === 0) uni.showToast({ title: '无符合记录', icon: 'none'});
      }, 300);
    }
  }
};
</script>

<style lang="scss">
// Conceptual Palette
// $uni-color-primary: #007AFF;
// $uni-color-error: #e54d42;
// $uni-text-color: #333333;
// $uni-text-color-grey: #666666;
// $uni-text-color-light: #555555;
// $uni-text-color-placeholder: #999999;
// $uni-bg-color-page: #f4f4f4;
// $uni-bg-color-white: #ffffff;
// $uni-border-color: #e0e0e0;
// $uni-border-color-light: #eeeeee;

// $uni-spacing-base: 20rpx;
// $uni-spacing-lg: 30rpx;
// $uni-card-border-radius: 16rpx;
// $uni-card-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
// $uni-button-height: 80rpx;
// $uni-button-font-size: 30rpx;
// $uni-input-height: 80rpx;
// $uni-input-border-radius: 8rpx;
// $uni-input-padding-h: 20rpx;

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
    margin-bottom: 0;
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

.empty-state {
  // Uses card-item
  text-align: center;
  color: #666666; // $uni-text-color-grey;
  padding: 60rpx 30rpx; // Increased padding
  font-size: 28rpx; // $uni-font-size-base;
}

.event-item {
  // Uses card-item
}
.event-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10rpx;
}
.event-type {
  font-size: 30rpx; // $uni-font-size-lg;
  font-weight: bold;
  color: #e54d42; // $uni-color-error; (Red for abnormal events)
}
.event-datetime {
  font-size: 26rpx;
  color: #666666; // $uni-text-color-grey;
}
.event-body {
  padding-top: 5rpx;
}
.event-description {
  font-size: 28rpx; // $uni-font-size-base;
  color: #555555; // $uni-text-color-light;
  line-height: 1.6;
}
</style>
