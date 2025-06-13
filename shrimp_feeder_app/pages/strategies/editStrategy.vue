<template>
  <view class="edit-strategy-page page-container"> <!-- Added page-container -->
    <form @submit.prevent="saveStrategy">
      <view class="form-item-card">
        <label class="form-label">策略名称</label>
        <input class="form-input" v-model="strategy.name" placeholder="如：晨间主力投喂" />
      </view>

      <view class="form-item-card">
        <label class="form-label">投喂时间</label>
        <picker mode="time" :value="strategy.time" @change="bindTimeChange">
          <view class="form-input picker-display">{{ strategy.time || '请选择时间' }}</view>
        </picker>
      </view>

      <view class="form-item-card">
        <label class="form-label">投喂量</label>
        <input class="form-input" type="number" v-model="strategy.amount" placeholder="例如: 2" />
      </view>

      <view class="form-item-card">
        <label class="form-label">单位</label>
        <picker :value="unitIndex" :range="unitOptions" range-key="text" @change="bindUnitChange">
          <view class="form-input picker-display">{{ unitOptions[unitIndex].text }}</view>
        </picker>
      </view>

      <view class="form-item-card">
        <label class="form-label">周期</label>
        <picker :value="cycleIndex" :range="cycleOptions" range-key="text" @change="bindCycleChange">
          <view class="form-input picker-display">{{ cycleOptions[cycleIndex].text }}</view>
        </picker>
      </view>

      <view v-if="strategy.cycleValue === 'weekly'" class="form-item-card">
        <label class="form-label full-width-label">选择星期 (可多选)</label>
        <checkbox-group @change="bindCycleDaysChange" class="checkbox-group">
          <label v-for="(day) in weekDays" :key="day.value" class="checkbox-item">
            <checkbox :value="day.value.toString()" :checked="strategy.cycleDays.includes(day.value)" color="#007AFF" style="transform:scale(0.85)"/>
            <text>{{ day.text }}</text>
          </label>
        </checkbox-group>
      </view>

      <view v-if="strategy.cycleValue === 'specific'" class="form-item-card">
        <label class="form-label">特定日期</label>
        <picker mode="date" :value="strategy.specificDate" @change="bindSpecificDateChange">
          <view class="form-input picker-display">{{ strategy.specificDate || '请选择日期' }}</view>
        </picker>
      </view>

      <button class="button save-button" form-type="submit" :disabled="isSaving || !isFormValid">保存策略</button>
      <button v-if="isEditMode" class="button delete-button" @tap="deleteStrategy" :disabled="isDeleting">删除策略</button>
    </form>
  </view>
</template>

<script>
// Script content largely remains the same, added computed for form validation
export default {
  data() {
    return {
      isEditMode: false,
      isSaving: false,
      isDeleting: false,
      strategy: {
        id: null,
        name: '',
        time: '08:00',
        amount: '',
        unit: '斤',
        cycleValue: 'daily',
        cycleText: '每天',
        cycleDays: [],
        specificDate: '',
        isEnabled: true
      },
      unitOptions: [
        { text: '斤', value: '斤' }, { text: '公斤', value: '公斤' }, { text: '克', value: '克' }
      ],
      unitIndex: 0,
      cycleOptions: [
        { text: '每天', value: 'daily' }, { text: '每周', value: 'weekly' }, { text: '特定日期', value: 'specific' }
      ],
      cycleIndex: 0,
      weekDays: [
        { text: '周一', value: 1 },{ text: '周二', value: 2 },{ text: '周三', value: 3 },
        { text: '周四', value: 4 },{ text: '周五', value: 5 },{ text: '周六', value: 6 },
        { text: '周日', value: 0 }
      ],
      originalStrategyId: null,
    };
  },
  computed: {
      isFormValid() {
          if (!this.strategy.name.trim() || !this.strategy.time || !this.strategy.amount || parseFloat(this.strategy.amount) <= 0) {
              return false;
          }
          if (this.strategy.cycleValue === 'weekly' && this.strategy.cycleDays.length === 0) {
              return false;
          }
          if (this.strategy.cycleValue === 'specific' && !this.strategy.specificDate) {
              return false;
          }
          return true;
      }
  },
  onLoad(options) {
    if (typeof getApp !== 'undefined' && !getApp().globalData.mockStrategies) {
        getApp().globalData.mockStrategies = [];
    }
    if (options.id) {
      this.isEditMode = true;
      this.originalStrategyId = options.id;
      this.loadStrategy(options.id);
    } else {
      this.isEditMode = false;
      this.strategy.id = 's' + Date.now();
      this.updateCycleText();
      this.updateUnitSelection();
    }
  },
  methods: {
    loadStrategy(id) {
      const strategies = getApp().globalData.mockStrategies || [];
      const foundStrategy = strategies.find(s => s.id === id);
      if (foundStrategy) {
        this.strategy = JSON.parse(JSON.stringify(foundStrategy));
        this.updateCycleSelection();
        this.updateUnitSelection();
      } else {
        uni.showToast({ title: '策略未找到', icon: 'error' });
        uni.navigateBack();
      }
    },
    updateCycleSelection() {
        this.cycleIndex = this.cycleOptions.findIndex(opt => opt.value === this.strategy.cycleValue);
        if(this.cycleIndex === -1) this.cycleIndex = 0; // Default to first if not found
    },
    updateUnitSelection() {
        this.unitIndex = this.unitOptions.findIndex(opt => opt.value === this.strategy.unit);
        if(this.unitIndex === -1) this.unitIndex = 0; // Default to first if not found
    },
    bindTimeChange(e) {
      this.strategy.time = e.detail.value;
    },
    bindUnitChange(e) {
      this.unitIndex = e.detail.value;
      this.strategy.unit = this.unitOptions[this.unitIndex].value;
    },
    bindCycleChange(e) {
      this.cycleIndex = e.detail.value;
      const selectedOpt = this.cycleOptions[this.cycleIndex];
      this.strategy.cycleValue = selectedOpt.value;
      this.strategy.cycleText = selectedOpt.text; // Base text

      if (this.strategy.cycleValue !== 'weekly') this.strategy.cycleDays = [];
      if (this.strategy.cycleValue !== 'specific') this.strategy.specificDate = '';
      this.updateCycleText(); // Update detailed text
    },
    bindCycleDaysChange(e) {
      this.strategy.cycleDays = e.detail.value.map(Number);
      this.updateCycleText();
    },
    bindSpecificDateChange(e) {
        this.strategy.specificDate = e.detail.value;
        this.updateCycleText();
    },
    updateCycleText() { // More robust cycleText generation
        const selectedCycleOption = this.cycleOptions[this.cycleIndex];
        if (selectedCycleOption.value === 'weekly') {
            if (this.strategy.cycleDays.length === 0) {
                this.strategy.cycleText = "每周 (请选择日期)";
            } else {
                // Sort days: Mon (1) ... Sun (0)
                const sortedDays = [...this.strategy.cycleDays].sort((a,b) => {
                    const dayOrder = [1,2,3,4,5,6,0]; // Mon to Sun
                    return dayOrder.indexOf(a) - dayOrder.indexOf(b);
                });
                let days = sortedDays
                    .map(dVal => this.weekDays.find(wd => wd.value === dVal)?.text)
                    .filter(Boolean)
                    .join(',');
                this.strategy.cycleText = `每周 (${days})`;
            }
        } else if (selectedCycleOption.value === 'specific') {
            this.strategy.cycleText = this.strategy.specificDate ? `特定日期 ${this.strategy.specificDate}` : '特定日期 (请选择)';
        } else {
            this.strategy.cycleText = selectedCycleOption.text;
        }
    },
    saveStrategy() {
      if (!this.isFormValid) { // Use computed property for cleaner check
          // Specific error messages can be generated based on which part of isFormValid is false
          uni.showToast({ title: '请完成所有必填项', icon: 'none' });
          return;
      }
      this.isSaving = true;
      let strategies = getApp().globalData.mockStrategies || [];
      if (this.isEditMode) {
        const index = strategies.findIndex(s => s.id === this.originalStrategyId);
        if (index !== -1) strategies[index] = { ...this.strategy, id: this.originalStrategyId };
        else strategies.push({...this.strategy}); // Fallback if not found, though unlikely
      } else {
        strategies.push({...this.strategy});
      }
      getApp().globalData.mockStrategies = strategies;
      uni.showToast({ title: '策略已保存', icon: 'success' });
      setTimeout(() => { uni.navigateBack(); this.isSaving = false; }, 1000);
    },
    deleteStrategy() {
      uni.showModal({
        title: '确认删除',
        content: `确定要删除策略 "${this.strategy.name}" 吗？`,
        success: (res) => {
          if (res.confirm) {
            this.isDeleting = true;
            let strategies = getApp().globalData.mockStrategies || [];
            getApp().globalData.mockStrategies = strategies.filter(s => s.id !== this.originalStrategyId);
            uni.showToast({ title: '策略已删除', icon: 'success' });
            setTimeout(() => { uni.navigateBack(); this.isDeleting = false; }, 1000);
          }
        }
      });
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
// $uni-text-color-placeholder: #999999;
// $uni-bg-color-page: #f4f4f4;
// $uni-bg-color-white: #ffffff;
// $uni-border-color: #e0e0e0;

// $uni-spacing-base: 20rpx;
// $uni-spacing-lg: 30rpx;
// $uni-card-border-radius: 10rpx; // Slightly less radius for form items
// $uni-button-height: 90rpx;
// $uni-button-font-size: 32rpx;
// $uni-button-border-radius: 10rpx;
// $uni-input-height: 80rpx; // Standard input height
// $uni-input-padding-h: 20rpx;
// $uni-input-border-radius: 8rpx;

.page-container {
  padding: 20rpx; // $uni-spacing-base; Less padding for form pages
  // background-color: #f4f4f4; // $uni-bg-color-page; // Set globally by page {}
  min-height: 100vh;
  box-sizing: border-box;
}

.form-item-card { // Each form group styled as a card
  background-color: #ffffff; // $uni-bg-color-white;
  padding: 20rpx 30rpx; // $uni-spacing-base $uni-spacing-lg;
  margin-bottom: 20rpx; // $uni-spacing-base;
  border-radius: 10rpx; // $uni-card-border-radius; // Or 16rpx for consistency with other cards
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap; // Allow wrapping for checkbox group
}

.form-label {
  font-size: 30rpx; // $uni-font-size-lg;
  color: #333333; // $uni-text-color;
  width: 180rpx; /* Adjusted width */
  margin-right: 20rpx; // $uni-spacing-base;
  flex-shrink: 0; /* Prevent label from shrinking */
}
.full-width-label { /* For labels above checkbox groups etc. */
    width: 100%;
    margin-bottom: 15rpx;
    color: #666666; // $uni-text-color-grey;
}


.form-input {
  flex: 1;
  min-width: 200rpx; /* Ensure input takes available space */
  height: 80rpx; // $uni-input-height;
  line-height: 80rpx; // Match height
  font-size: 30rpx; // $uni-font-size-lg;
  color: #555555;
  text-align: right;
  border: 1rpx solid #e0e0e0; // $uni-border-color;
  border-radius: 8rpx; // $uni-input-border-radius;
  padding: 0 20rpx; // $uni-input-padding-h;
  background-color: #ffffff; // $uni-bg-color-white;
  box-sizing: border-box;
}
.picker-display { // Class for the view inside picker
  @extend .form-input; // Inherit styles from form-input
  // text-align: right; // Already in form-input
  // color: #555555; // Already in form-input
  // Placeholder color handled by text content like '请选择时间'
  &::after { // Remove default picker arrow if custom is desired or to match input
    // display: none;
  }
}
input::placeholder {
    color: #999999; // $uni-text-color-placeholder;
}


.checkbox-group {
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start; /* Align to start for better multi-line layout */
    flex: 1; /* Take remaining space */
    padding: 10rpx 0; /* Add some padding */
}
.checkbox-item {
    font-size: 28rpx; // $uni-font-size-base;
    color: #333333; // $uni-text-color;
    margin-right: 20rpx; // $uni-spacing-base;
    margin-bottom: 10rpx; // $uni-spacing-sm;
    display: flex;
    align-items: center;
}
.checkbox-item text {
    margin-left: 5rpx;
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

.save-button {
  @extend .button;
  width: 100%;
  background-color: #007AFF; // $uni-color-primary; /* Changed from green to primary */
  color: white;
  margin-top: 40rpx;
  height: 90rpx; // $uni-button-height;
  font-size: 32rpx; // $uni-button-font-size;
  line-height: 90rpx;
  border-radius: 10rpx; // $uni-button-border-radius;

  &[disabled] {
    background-color: #a0cfff; // Lighter primary
    opacity: 0.7;
  }
}

.delete-button {
  @extend .button;
  width: 100%;
  background-color: #e54d42; // $uni-color-error;
  color: white;
  margin-top: 30rpx; // $uni-spacing-lg;
  height: 90rpx; // $uni-button-height;
  font-size: 32rpx; // $uni-button-font-size;
  line-height: 90rpx;
  border-radius: 10rpx; // $uni-button-border-radius;

  &[disabled] {
    background-color: #f7b2ae; // Lighter error
    opacity: 0.7;
  }
}
</style>
