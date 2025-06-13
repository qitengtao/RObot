<template>
  <view class="login-container">
    <!-- App Logo Placeholder -->
    <view class="logo-placeholder">
      <text>App Logo</text>
    </view>

    <!-- Input field for Phone Number -->
    <input class="input-field" type="number" placeholder="请输入手机号码" v-model="phoneNumber" />

    <!-- Input field for Verification Code & Button -->
    <view class="verification-code-container">
      <input class="input-field verification-code-input" type="number" placeholder="请输入验证码" v-model="verificationCode" />
      <button class="button get-code-button" @tap="getVerificationCode">获取验证码</button>
    </view>

    <!-- Button for Login / Register -->
    <button class="button login-button" @tap="loginOrRegister">登录 / 注册</button>

    <!-- Terms of Service / Privacy Policy Placeholder -->
    <view class="terms-placeholder">
      <text>登录即代表同意《用户服务协议》和《隐私政策》</text>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      phoneNumber: '',
      verificationCode: ''
    }
  },
  methods: {
    getVerificationCode() {
      // Placeholder for getting verification code logic
      console.log('Get verification code for:', this.phoneNumber);
      uni.showToast({
        title: '验证码已发送（模拟）',
        icon: 'none'
      });
    },
    loginOrRegister() {
      // Placeholder for login/registration logic
      console.log('Login/Register with:', this.phoneNumber, this.verificationCode);
      uni.showToast({
        title: '登录成功（模拟）',
        icon: 'none'
      });
      // Navigate to home page after successful login (simulation)
      // uni.switchTab({
      //  url: '/pages/home/home' // Or the main tab page
      // });
    }
  }
}
</script>

<style lang="scss">
/* Color Palette (Conceptual - not using SCSS vars due to tool limits) */
// $uni-color-primary: #007AFF;
// $uni-color-success: #3cc51f;
// $uni-text-color-placeholder: #999999;
// $uni-border-color: #e0e0e0;
// $uni-bg-color-page: #f4f4f4; // Assuming page background is set globally or on body

.login-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40rpx; /* Consistent page padding */
  box-sizing: border-box;
  min-height: 100vh; /* Ensure full height */
  // background-color: $uni-bg-color-page; /* If not set globally */
}

.logo-placeholder {
  width: 160rpx; /* Standardized size */
  height: 160rpx;
  background-color: #f0f0f0; /* Light placeholder color */
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 50rpx;
  margin-bottom: 70rpx; /* Adjusted spacing */
  border-radius: 16rpx; /* Standardized card/element radius */
}

.logo-placeholder text {
  color: #999999; // $uni-text-color-placeholder;
}

.input-field {
  width: 100%;
  height: 90rpx; /* Standardized input height */
  border: 1rpx solid #e0e0e0; // $uni-border-color;
  border-radius: 8rpx; /* Standardized input radius */
  padding: 0 20rpx; /* Standardized input padding */
  margin-bottom: 30rpx;
  font-size: 30rpx;
  box-sizing: border-box;
  background-color: #ffffff;
}

.verification-code-container {
  display: flex;
  width: 100%;
  margin-bottom: 30rpx;
  align-items: center;
}

.verification-code-input {
  flex-grow: 1;
  margin-bottom: 0;
  margin-right: 20rpx; /* Spacing between input and button */
}

/* General button class for consistent active state if needed, or apply directly */
.button {
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  &:active {
    opacity: 0.8;
  }
}

.get-code-button {
  height: 90rpx; /* Standardized height */
  font-size: 28rpx; /* Slightly smaller for secondary actions */
  background-color: #3cc51f; // $uni-color-success;
  color: white;
  padding: 0 30rpx;
  border-radius: 8rpx; /* Standardized radius */
  white-space: nowrap;
  border: none; /* Explicitly remove border for solid buttons */
}

.login-button {
  width: 100%;
  height: 90rpx; /* Standardized height */
  background-color: #007AFF; // $uni-color-primary;
  color: white;
  font-size: 32rpx; /* Standard primary button font size */
  border-radius: 8rpx; /* Standardized radius */
  margin-top: 40rpx; /* Increased spacing before primary action */
  margin-bottom: 40rpx;
  border: none; /* Explicitly remove border */
}

.terms-placeholder {
  font-size: 24rpx;
  color: #999999; // $uni-text-color-placeholder;
  text-align: center;
  line-height: 1.5;
}
</style>
