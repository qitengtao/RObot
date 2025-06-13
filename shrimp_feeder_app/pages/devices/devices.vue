<template>
  <view class="devices-page-container">
    <view v-if="devices.length === 0" class="empty-state">
      <text>暂无设备，请点击下方按钮绑定</text>
    </view>

    <view v-for="(device, index) in devices" :key="device.id" class="device-card card-item">
      <view class="device-header">
        <text class="device-name">{{ device.name }} ({{ device.id }})</text>
        <text :class="['device-status', device.onlineStatus ? 'status-online' : 'status-offline']">
          {{ device.onlineStatus ? '在线' : '离线' }}
        </text>
      </view>

      <view class="device-info">
        <text>最后投喂: {{ device.lastFeedTime }}</text>
        <text v-if="device.isFeeding" class="feeding-status status-warning">投喂中...</text>
      </view>

      <view class="device-controls">
        <view class="control-item">
          <text class="control-label">启停:</text>
          <switch :checked="device.isFeeding" @change="toggleDevicePower(index)" color="#3cc51f" style="transform:scale(0.85)" />
        </view>
        <button class="button control-button manual-feed-button" @tap="openManualFeedModal(index)" :disabled="!device.onlineStatus">手动投喂</button>
        <button class="button control-button unbind-button" @tap="unbindDevice(index)">解绑</button>
      </view>
    </view>

    <!-- Manual Feed Modal -->
    <view v-if="showManualFeedModal" class="modal-overlay" @tap="closeManualFeedModal">
      <view class="modal-content card-item" @tap.stop> <!-- Added card-item for consistent modal bg/shadow if needed -->
        <text class="modal-title">手动投喂 - {{ currentDeviceForFeed ? currentDeviceForFeed.name : '' }}</text>
        <input class="modal-input input-field-style" type="number" v-model="manualFeedWeight" placeholder="请输入投喂重量 (斤)" />
        <view class="modal-actions">
          <button class="button modal-button cancel-button" @tap="closeManualFeedModal">取消</button>
          <button class="button modal-button confirm-button" @tap="confirmManualFeed">确认</button>
        </view>
      </view>
    </view>

    <!-- Bind New Device Button -->
    <button class="button fab-add-device" @tap="navigateBindDevice">
      <uni-icons type="plusempty" size="24" color="#fff"></uni-icons>
    </button>

  </view>
</template>

<script>
// Script content remains the same
export default {
  data() {
    return {
      devices: [
        { id: 'SN001', name: '1号投料机', onlineStatus: true, lastFeedTime: '2023-10-27 10:00', isFeeding: false },
        { id: 'SN002', name: '2号投料机', onlineStatus: false, lastFeedTime: '2023-10-26 18:00', isFeeding: false },
        { id: 'SN003', name: '3号投料机', onlineStatus: true, lastFeedTime: '2023-10-27 12:00', isFeeding: true }
      ],
      showManualFeedModal: false,
      manualFeedWeight: '',
      currentDeviceIndexForFeed: null,
    }
  },
  computed: {
    currentDeviceForFeed() {
      if (this.currentDeviceIndexForFeed !== null && this.devices[this.currentDeviceIndexForFeed]) {
        return this.devices[this.currentDeviceIndexForFeed];
      }
      return null;
    }
  },
  methods: {
    navigateBindDevice() {
      uni.navigateTo({
        url: '/pages/devices/bindDevice'
      });
    },
    toggleDevicePower(index) {
      const device = this.devices[index];
      if (!device.onlineStatus && !device.isFeeding) {
        uni.showToast({ title: `${device.name} 已离线，无法操作`, icon: 'none' });
        return;
      }
      device.isFeeding = !device.isFeeding;
      uni.showToast({
        title: `${device.name} 已${device.isFeeding ? '开启' : '关闭'}`,
        icon: 'none'
      });
    },
    openManualFeedModal(index) {
      this.currentDeviceIndexForFeed = index;
      this.manualFeedWeight = '';
      this.showManualFeedModal = true;
    },
    closeManualFeedModal() {
      this.showManualFeedModal = false;
      this.currentDeviceIndexForFeed = null;
    },
    confirmManualFeed() {
      if (!this.manualFeedWeight || isNaN(parseFloat(this.manualFeedWeight)) || parseFloat(this.manualFeedWeight) <= 0) {
        uni.showToast({ title: '请输入有效的投喂重量', icon: 'none' });
        return;
      }
      const device = this.currentDeviceForFeed;
      if (device) {
        uni.showToast({
          title: `向 ${device.name} 手动投喂 ${this.manualFeedWeight}斤 命令已发送`,
          icon: 'none',
          duration: 2000
        });
        device.isFeeding = true;
        device.lastFeedTime = new Date().toISOString().slice(0, 16).replace('T', ' ');
      }
      this.closeManualFeedModal();
    },
    unbindDevice(index) {
      const deviceToUnbind = this.devices[index];
      uni.showModal({
        title: '确认解绑',
        content: `确定要解绑设备 ${deviceToUnbind.name} (${deviceToUnbind.id}) 吗？`,
        success: (res) => {
          if (res.confirm) {
            this.devices.splice(index, 1);
            uni.showToast({
              title: `设备 ${deviceToUnbind.name} 已解绑`,
              icon: 'success'
            });
          }
        }
      });
    }
  }
}
</script>

<style lang="scss">
// Conceptual Palette
// $uni-color-primary: #007AFF;
// $uni-color-success: #3cc51f;
// $uni-color-error: #e54d42;
// $uni-color-warning: #f39c12;
// $uni-text-color: #333333;
// $uni-text-color-grey: #666666;
// $uni-text-color-placeholder: #999999;
// $uni-bg-color-page: #f4f4f4;
// $uni-bg-color-white: #ffffff;
// $uni-border-color: #e0e0e0;
// $uni-border-color-light: #eeeeee;

// $uni-spacing-base: 20rpx;
// $uni-spacing-lg: 30rpx;
// $uni-card-border-radius: 16rpx;
// $uni-card-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
// $uni-button-small-height: 60rpx;
// $uni-button-small-font-size: 26rpx;
// $uni-input-border-radius: 8rpx;

.devices-page-container {
  padding: 20rpx; // $uni-spacing-base;
  // background-color: #f4f4f4; // $uni-bg-color-page; // Set globally
  min-height: 100vh;
  padding-bottom: 140rpx; /* Space for FAB, increased slightly */
  box-sizing: border-box;
}

.empty-state {
  text-align: center;
  padding-top: 150rpx; /* Adjusted padding */
  color: #999999; // $uni-text-color-placeholder;
  font-size: 30rpx;
}

.card-item { // Common card style
  background-color: #ffffff; // $uni-bg-color-white;
  border-radius: 16rpx; // $uni-card-border-radius;
  padding: 24rpx; // Slightly less than page padding for visual hierarchy
  margin-bottom: 20rpx; // $uni-spacing-base;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05); // $uni-card-shadow;
}

.device-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16rpx;
  padding-bottom: 16rpx;
  border-bottom: 1rpx solid #eeeeee; // $uni-border-color-light;
}

.device-name {
  font-size: 32rpx;
  font-weight: bold;
  color: #333333; // $uni-text-color;
}

.device-status {
  font-size: 26rpx; /* Slightly smaller status text */
  padding: 8rpx 16rpx; /* Adjusted padding */
  border-radius: 8rpx; // $uni-input-border-radius
  color: #ffffff; // $uni-bg-color-white;
}
.status-online { background-color: #3cc51f; } // $uni-color-success
.status-offline { background-color: #e54d42; } // $uni-color-error
.status-warning { color: #f39c12; } // $uni-color-warning, used for feeding status text

.device-info {
  font-size: 28rpx; // $uni-font-size-base
  color: #666666; // $uni-text-color-grey;
  margin-bottom: 20rpx; // $uni-spacing-base;
  line-height: 1.5;
}
.device-info text {
    display: block;
    margin-bottom: 5rpx;
}

.feeding-status { // Already has status-warning class for color
  font-weight: bold;
  margin-top: 8rpx;
}

.device-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  margin-top: 10rpx; /* Added margin for spacing */
}

.control-item {
  display: flex;
  align-items: center;
  margin-right: 10rpx;
  margin-bottom: 10rpx;
}
.control-label {
  font-size: 28rpx; // $uni-font-size-base;
  color: #333333; // $uni-text-color;
  margin-right: 10rpx;
}

/* General button class for consistent active state */
.button {
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  border: none; /* Remove default borders */
  &:active {
    opacity: 0.8;
  }
}

.control-button {
  font-size: 26rpx; // $uni-button-small-font-size;
  padding: 0 25rpx; /* Adjusted padding */
  height: 64rpx; // $uni-button-small-height + 4rpx for better touch
  line-height: 64rpx;
  border-radius: 8rpx; // $uni-input-border-radius;
  margin: 5rpx;
  min-width: 130rpx; /* Adjusted min-width */
  text-align: center;
}

.manual-feed-button {
  background-color: #007AFF; // $uni-color-primary;
  color: white;
  &[disabled] { // More specific disabled style
    background-color: #a0cfff; // Lighter primary
    color: #e0e0e0; // Lighter text
    opacity: 0.7;
  }
}

.unbind-button {
  background-color: #e54d42; // $uni-color-error; (using error for destructive)
  color: white;
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal-content { // Uses .card-item for base styling
  width: 85%; /* Slightly wider modal */
  max-width: 600rpx;
  padding: 30rpx; // $uni-spacing-lg
}

.modal-title {
  font-size: 34rpx;
  font-weight: bold;
  text-align: center;
  margin-bottom: 30rpx; // $uni-spacing-lg;
  color: #333333; // $uni-text-color;
}

.input-field-style { // Common style for inputs
  width: 100%;
  height: 80rpx; // $uni-input-height;
  border: 1rpx solid #e0e0e0; // $uni-border-color;
  border-radius: 8rpx; // $uni-input-border-radius;
  padding: 0 20rpx; // $uni-input-padding-h;
  font-size: 30rpx; // $uni-font-size-lg;
  box-sizing: border-box;
  background-color: #ffffff; // $uni-bg-color-white;
}
.modal-input { // Specific margin for modal input
  @extend .input-field-style;
  margin-bottom: 40rpx; // $uni-spacing-xl;
}


.modal-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 10rpx; /* Added margin */
}

.modal-button {
  flex: 1; // Take equal space
  height: 80rpx; // $uni-input-height
  font-size: 30rpx; // $uni-font-size-lg
  border-radius: 8rpx; // $uni-input-border-radius
  margin: 0 10rpx; /* Add horizontal margin */
}
.modal-button:first-child { margin-left: 0; }
.modal-button:last-child { margin-right: 0; }


.cancel-button {
  background-color: #f0f0f0; // Light grey for cancel
  color: #333333; // $uni-text-color;
}

.confirm-button {
  background-color: #007AFF; // $uni-color-primary;
  color: white;
}

.fab-add-device {
  position: fixed;
  bottom: 50rpx; /* Adjusted position */
  right: 30rpx; // $uni-spacing-lg;
  width: 100rpx;
  height: 100rpx;
  background-color: #007AFF; // $uni-color-primary;
  color: white;
  border-radius: 50rpx; /* Perfect circle */
  box-shadow: 0 8rpx 20rpx rgba(0, 0, 0, 0.2);
  z-index: 100;
}
</style>
