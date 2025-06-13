<template>
  <view class="bind-device-page page-container"> <!-- Added page-container for consistent padding -->
    <view class="form-section card-item">
      <view class="form-group">
        <label class="form-label" for="deviceIdInput">设备编号:</label>
        <input id="deviceIdInput" class="form-input input-field-style" v-model="deviceId" placeholder="请输入设备SN码或ID" />
      </view>
      <button class="button bind-button" @tap="handleBindDevice" :disabled="isBinding || !deviceId.trim()">
        {{ isBinding ? '绑定中...' : '绑定设备' }}
      </button>
    </view>

    <view class="instructions-card card-item">
        <view class="section-title-bar">绑定说明</view> <!-- Using a more generic section title class -->
        <text class="instructions-text">1. 请确保设备已开机并连接到网络。</text>
        <text class="instructions-text">2. 输入设备外壳上或说明书中的设备编号 (SN码)。</text>
        <text class="instructions-text">3. 点击“绑定设备”按钮，等待系统确认。</text>
        <text class="instructions-text">4. 如遇问题，请联系客服支持。</text>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      deviceId: '',
      isBinding: false
    };
  },
  methods: {
    handleBindDevice() {
      if (!this.deviceId.trim()) {
        uni.showToast({
          title: '请输入设备编号',
          icon: 'none'
        });
        return;
      }
      this.isBinding = true;
      uni.showLoading({ title: '正在绑定...' });
      setTimeout(() => {
        this.isBinding = false;
        uni.hideLoading();
        uni.showToast({
          title: `设备 ${this.deviceId} 绑定成功`,
          icon: 'success',
          duration: 2000
        });
        this.deviceId = '';
        // setTimeout(() => { uni.navigateBack(); }, 2000);
      }, 1500);
    }
  }
};
</script>

<style lang="scss">
// Conceptual Palette & Variables (placeholders, not from uni.scss due to tool limits)
// $uni-color-primary: #007AFF;
// $uni-text-color: #333333;
// $uni-text-color-grey: #666666;
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
// $uni-input-height: 90rpx;
// $uni-input-border-radius: 8rpx;
// $uni-input-padding-h: 20rpx;

.page-container { // Standard page padding
  padding: 30rpx; // $uni-spacing-lg;
  box-sizing: border-box;
  min-height: 100vh;
  // background-color: #f4f4f4; // $uni-bg-color-page; // Set globally by page {}
}

.card-item { // Common card style
  background-color: #ffffff; // $uni-bg-color-white;
  border-radius: 16rpx; // $uni-card-border-radius;
  padding: 30rpx; // $uni-spacing-lg;
  margin-bottom: 30rpx; // $uni-spacing-lg;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
}

.form-section { // Specific to this page if needed, otherwise use card-item directly
}

.form-group {
  margin-bottom: 30rpx; // $uni-spacing-lg;
}

.form-label {
  display: block;
  font-size: 30rpx; // $uni-font-size-lg;
  color: #333333; // $uni-text-color;
  margin-bottom: 15rpx;
}

.input-field-style { // Common style for inputs
  width: 100%;
  height: 90rpx; // $uni-input-height;
  border: 1rpx solid #e0e0e0; // $uni-border-color;
  border-radius: 8rpx; // $uni-input-border-radius;
  padding: 0 20rpx; // $uni-input-padding-h;
  font-size: 30rpx; // $uni-font-size-lg;
  box-sizing: border-box;
  background-color: #ffffff; // $uni-bg-color-white;
}
.form-input { // Inherits from input-field-style if that class is applied
   @extend .input-field-style; // If SCSS was fully working
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

.bind-button {
  @extend .button; // If SCSS was fully working
  width: 100%;
  background-color: #007AFF; // $uni-color-primary;
  color: white;
  height: 90rpx; // $uni-button-height;
  font-size: 32rpx; // $uni-button-font-size;
  line-height: 90rpx; // Match height
  border-radius: 10rpx; // $uni-button-border-radius;

  &[disabled] { // More specific disabled style
    background-color: #a0cfff; // Lighter primary
    // color: #e0e0e0; // Lighter text, or default browser disabled text color
    opacity: 0.7; /* Standard way to show disabled state */
  }
}


.instructions-card {} // Uses .card-item

.section-title-bar { // From conceptual global styles
  font-size: 30rpx; // $uni-font-size-lg or title
  font-weight: bold;
  color: #333333; // $uni-text-color;
  margin-bottom: 20rpx; // $uni-spacing-base;
  padding-bottom: 15rpx; // $uni-spacing-base / 2
  border-bottom: 1rpx solid #eeeeee; // $uni-border-color-light;
}

.instructions-text {
    display: block;
    font-size: 28rpx; // $uni-font-size-base;
    color: #666666; // $uni-text-color-grey;
    line-height: 1.7; /* Improved line height for readability */
    margin-bottom: 15rpx; /* Consistent spacing */
}
.instructions-text:last-child {
    margin-bottom: 0;
}
</style>
