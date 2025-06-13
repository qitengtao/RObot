<template>
  <view class="feeding-log-page page-container"> <!-- Added page-container -->
    <view class="filter-section card-item"> <!-- Used card-item -->
      <view class="date-range-buttons">
        <button :class="['button', 'range-button', activeRange === 'today' ? 'active' : '']" @tap="setRange('today')">今天</button>
        <button :class="['button', 'range-button', activeRange === 'week' ? 'active' : '']" @tap="setRange('week')">本周</button>
        <button :class="['button', 'range-button', activeRange === 'month' ? 'active' : '']" @tap="setRange('month')">本月</button>
      </view>
      <view class="custom-date-pickers">
        <picker mode="date" :value="startDate" :end="endDate || today" @change="bindStartDateChange">
          <view class="picker-display form-input-style">{{ startDate || '开始日期' }}</view>
        </picker>
        <text class="date-separator">至</text>
        <picker mode="date" :value="endDate" :start="startDate || '1970-01-01'" :end="today" @change="bindEndDateChange">
          <view class="picker-display form-input-style">{{ endDate || '结束日期' }}</view>
        </picker>
      </view>
      <button class="button query-button" @tap="queryLogs">查询日志</button>
    </view>

    <view v-if="filteredLogs.length === 0" class="empty-state card-item"> <!-- Used card-item -->
      <text>当前筛选条件下无投喂记录</text>
    </view>

    <view v-for="log in filteredLogs" :key="log.id" class="log-item card-item"> <!-- Used card-item -->
      <view class="log-header">
        <text class="log-strategy">{{ log.strategyName }}</text>
        <text class="log-datetime">{{ log.dateTime }}</text>
      </view>
      <view class="log-body">
        <text class="log-amount">投喂量: {{ log.amount }} {{ log.unit }}</text>
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
      activeRange: 'today',
      allLogs: [
        { id: 'fl1', dateTime: '2023-10-28 08:00', strategyName: '晨间投喂', amount: '2', unit: '斤' },
        { id: 'fl2', dateTime: '2023-10-28 12:30', strategyName: '手动投喂', amount: '0.5', unit: '斤' },
        { id: 'fl4', dateTime: `${today} 09:00`, strategyName: '今日主力', amount: '2.2', unit: '斤' },
        { id: 'fl5', dateTime: `${new Date(Date.now() - 86400000).toISOString().slice(0,10)} 10:00`, strategyName: '昨日测试', amount: '1.0', unit: '斤' },
        { id: 'fl3', dateTime: '2023-10-27 17:30', strategyName: '傍晚加餐', amount: '1.5', unit: '斤' }
      ],
      filteredLogs: []
    };
  },
  created() { this.queryLogs(); },
  methods: {
    bindStartDateChange(e) { this.startDate = e.detail.value; this.activeRange = 'custom'; },
    bindEndDateChange(e) { this.endDate = e.detail.value; this.activeRange = 'custom'; },
    setRange(range) {
      this.activeRange = range;
      const todayDate = new Date();
      if (range === 'today') {
        this.startDate = this.today; this.endDate = this.today;
      } else if (range === 'week') {
        const dayOfWeek = todayDate.getDay();
        const startDate = new Date(todayDate);
        startDate.setDate(todayDate.getDate() - (dayOfWeek === 0 ? 6 : dayOfWeek - 1));
        this.startDate = startDate.toISOString().slice(0, 10);
        this.endDate = this.today;
      } else if (range === 'month') {
        this.startDate = new Date(todayDate.getFullYear(), todayDate.getMonth(), 1).toISOString().slice(0, 10);
        this.endDate = this.today;
      }
      this.queryLogs();
    },
    queryLogs() {
      uni.showLoading({ title: '查询中...' });
      setTimeout(() => {
        this.filteredLogs = this.allLogs.filter(log => {
          const logDate = new Date(log.dateTime.split(' ')[0]);
          const start = new Date(this.startDate);
          const end = new Date(this.endDate);
          return logDate >= start && logDate <= end;
        }).sort((a, b) => new Date(b.dateTime) - new Date(a.dateTime));
        uni.hideLoading();
        if(this.filteredLogs.length === 0) uni.showToast({ title: '无符合记录', icon: 'none'});
      }, 300); // Reduced timeout for faster UI feedback
    }
  }
};
</script>

<style lang="scss">
// Conceptual Palette
// $uni-color-primary: #007AFF;
// $uni-text-color: #333333;
// $uni-text-color-grey: #666666;
// $uni-text-color-light: #555555; // For log amount
// $uni-text-color-placeholder: #999999;
// $uni-bg-color-page: #f4f4f4;
// $uni-bg-color-white: #ffffff;
// $uni-border-color: #e0e0e0;
// $uni-border-color-light: #eeeeee;

// $uni-spacing-base: 20rpx;
// $uni-spacing-lg: 30rpx;
// $uni-card-border-radius: 16rpx;
// $uni-card-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
// $uni-button-height-small: 70rpx; // For range buttons
// $uni-button-height: 80rpx; // For query button
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

.date-range-buttons {
  display: flex;
  justify-content: space-around;
  margin-bottom: 30rpx; // $uni-spacing-lg;
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

.range-button {
  @extend .button;
  font-size: 28rpx;
  padding: 0 25rpx; /* Adjusted padding */
  height: 70rpx; // $uni-button-height-small;
  line-height: 70rpx;
  background-color: #f0f0f0; // Lighter background for non-active
  color: #333333; // $uni-text-color;
  border-radius: 8rpx; // $uni-input-border-radius;
  margin: 0 5rpx;

  &.active {
    background-color: #007AFF; // $uni-color-primary;
    color: white;
    font-weight: 500; /* Make active button text slightly bolder */
  }
}

.custom-date-pickers {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 30rpx; // $uni-spacing-lg;
}

.form-input-style { // Common style for inputs / picker displays
  height: 80rpx; // $uni-input-height;
  line-height: 80rpx;
  border: 1rpx solid #e0e0e0; // $uni-border-color;
  border-radius: 8rpx; // $uni-input-border-radius;
  padding: 0 20rpx; // $uni-input-padding-h;
  font-size: 28rpx;
  background-color: #ffffff; // $uni-bg-color-white;
  color: #555555;
  text-align: center; /* Centered text for date pickers */
}
.picker-display { // Applied to the view within picker
   @extend .form-input-style;
   width: 280rpx; /* Adjusted width to fit typical date strings */
}
input::placeholder, .picker-display:empty::before {
    color: #999999; // $uni-text-color-placeholder;
}

.date-separator {
  margin: 0 10rpx;
  font-size: 28rpx;
  color: #666666; // $uni-text-color-grey;
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
}

.empty-state {
  // Uses card-item
  text-align: center;
  color: #666666; // $uni-text-color-grey;
  padding: 60rpx 30rpx; // Increased padding
  font-size: 28rpx; // $uni-font-size-base;
}

.log-item {
  // Uses card-item
}
.log-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15rpx; // Adjusted margin
  padding-bottom: 15rpx; // Adjusted padding
  border-bottom: 1rpx solid #eeeeee; // $uni-border-color-light;
}
.log-strategy {
  font-size: 30rpx; // $uni-font-size-lg;
  font-weight: bold;
  color: #333333; // $uni-text-color;
}
.log-datetime {
  font-size: 26rpx;
  color: #666666; // $uni-text-color-grey;
}
.log-body {
  padding-top: 5rpx; /* Added padding */
}
.log-amount {
  font-size: 28rpx; // $uni-font-size-base;
  color: #555555; // $uni-text-color-light;
}
</style>
