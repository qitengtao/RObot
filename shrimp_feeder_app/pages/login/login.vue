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
      <button class="get-code-button" @tap="getVerificationCode">获取验证码</button>
    </view>

    <!-- Button for Login / Register -->
    <button class="login-button" @tap="loginOrRegister">登录 / 注册</button>

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
      //  url: '/pages/home/home'
      // });
    }
  }
}
</script>

<style>
.login-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40rpx;
  box-sizing: border-box;
  height: 100vh;
}

.logo-placeholder {
  width: 150rpx;
  height: 150rpx;
  background-color: #f0f0f0;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 50rpx;
  margin-bottom: 80rpx;
  border-radius: 20rpx; /* Optional: makes it a bit more logo-like */
}

.logo-placeholder text {
  color: #999;
}

.input-field {
  width: 100%;
  height: 100rpx;
  border: 1rpx solid #e0e0e0;
  border-radius: 10rpx;
  padding: 0 30rpx;
  margin-bottom: 30rpx;
  font-size: 30rpx;
  box-sizing: border-box;
}

.verification-code-container {
  display: flex;
  width: 100%;
  margin-bottom: 30rpx;
  align-items: center;
}

.verification-code-input {
  flex-grow: 1;
  margin-bottom: 0; /* Reset margin-bottom from .input-field */
  margin-right: 20rpx;
}

.get-code-button {
  height: 100rpx;
  font-size: 28rpx;
  background-color: #3cc51f; /* uni-app green */
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0 30rpx;
  border-radius: 10rpx;
  white-space: nowrap; /* Prevents text wrapping */
}

.login-button {
  width: 100%;
  height: 100rpx;
  background-color: #007aff; /* uni-app blue */
  color: white;
  font-size: 32rpx;
  border-radius: 10rpx;
  margin-top: 20rpx;
  margin-bottom: 40rpx;
}

.terms-placeholder {
  font-size: 24rpx;
  color: #999;
  text-align: center;
}
</style>
