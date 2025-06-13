<template>
  <view class="profile-container">
    <!-- User Avatar Placeholder -->
    <view class="avatar-placeholder">
      <!-- You can replace this with an <image> component once you have an avatar source -->
      <text class="avatar-text">头像</text>
    </view>

    <!-- Nickname Display -->
    <text class="nickname-text">{{ nickname }}</text>

    <!-- (Optional) Other Info - Example: User ID -->
    <view class="info-item">
      <text class="info-label">User ID:</text>
      <text class="info-value">{{ userId }}</text>
    </view>

    <!-- Logout Button -->
    <button class="logout-button" @tap="handleLogout">退出登录</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      nickname: '微信用户', // Placeholder nickname
      userId: 'user_placeholder_12345' // Placeholder user ID
    }
  },
  onLoad() {
    // Lifecycle hook
    // You might fetch user data here in a real application
  },
  methods: {
    handleLogout() {
      console.log('Logout button tapped');
      uni.showToast({
        title: '已退出登录',
        icon: 'none',
        duration: 1500
      });

      // Navigate to login page after a short delay to allow toast to be seen
      setTimeout(() => {
        uni.reLaunch({
          url: '/pages/login/login' // Ensure this path matches your login page route
        });
      }, 1500);
    }
  }
}
</script>

<style>
.profile-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 80rpx; /* Add some padding at the top */
  padding-left: 40rpx;
  padding-right: 40rpx;
  box-sizing: border-box;
}

.avatar-placeholder {
  width: 180rpx;
  height: 180rpx;
  border-radius: 50%; /* Makes it circular */
  background-color: #e0e0e0; /* Light grey background */
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 30rpx;
}

.avatar-text {
  color: #999;
  font-size: 32rpx;
}

.nickname-text {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 40rpx;
}

.info-item {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  width: 100%;
  padding: 20rpx 0;
  border-bottom: 1rpx solid #f0f0f0; /* Light separator line */
  margin-bottom: 20rpx;
}

.info-label {
  font-size: 30rpx;
  color: #666;
}

.info-value {
  font-size: 30rpx;
  color: #333;
}

.logout-button {
  width: 100%;
  height: 90rpx;
  background-color: #ff4d4f; /* A common color for logout/danger actions */
  color: white;
  font-size: 32rpx;
  border-radius: 10rpx;
  margin-top: 60rpx; /* Add some space above the button */
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
