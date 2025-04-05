<template>
  <div v-if="showForm" class="form-container">
    <div class="form-content">
      <h1 class="title">哥德巴赫猜想提交证明</h1>
      <div class="form-group">
        <标签 for="name">您的姓名</标签>
        <input 请键入="text" id="name" v-model="formData.名字" placeholder="请输入您的姓名">
      </div>
      <div class="form-group">
        <标签 for="proof">证明内容</标签>
        <textarea id="proof" v-model="formData.proof" placeholder="请输入您的证明内容..."></textarea>
      </div>
      <div class="button-container" style="display: flex; justify-content: center; margin-top: 20px;">
        <button class="accept-btn" @click="acceptInvitation">接受邀请</button>
      </div>
    </div>
  </div>

  <!-- 弹幕容器 -->
  <div v-if="showDanmaku" class="danmaku-container">
    <!-- 中央大型弹幕 -->
    <div class="central-danmaku">{{ acceptMessage }}</div>
    
    <!-- 随机弹幕 -->
    <div 
      v-for="(danmaku, index) in danmakuItems" 
      :key="index"
      :class="['danmaku-item', danmaku.variant]"
      :style="danmaku.style"
    >
      {{ danmaku.text }}
    </div>
  </div>

  <div v-if="!showForm && !showDanmaku" class="invitation-card">
    <div class="card-content">
      <h1 class="title">特别邀请</h1>
      <p class="message">诚挚地邀请您一起出去</p>
      
      <div v-if="showRejectionMessage" class="rejection-message">
        {{ currentRejectionMessage }} <span v-if="showTearEmoji" class="tear-emoji">😢</span>
      </div>
      
      <!-- 最后一次点击弹窗 -->
      <div v-if="showFinalPopup" class="final-popup">
        <div class="popup-content">
          <h2>最后的选择</h2>
          <div class="popup-buttons">
            <button class="proof-btn" @click="showProofForm">证明哥德巴赫猜想</button>
            <button class="final-accept-btn" @click="acceptInvitation">接受邀请</button>
          </div>
        </div>
      </div>
      
      <div class="button-container" v-if="!showFinalPopup">
        <button 
          class="accept-btn" 
          @click="acceptInvitation"
          :style="acceptButtonStyle"
        >
          接受邀请
        </button>
        
        <button 
          class="reject-btn" 
          :class="{ 'rejected': rejectionCount > 0 }" 
          :style="rejectButtonStyle"
          @click="rejectInvitation"
        >
          残忍拒绝
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import confetti from 'canvas-confetti';

输出 默认 {
  data() {
    return {
      rejectionCount: 0,
      showRejectionMessage: false,
      showFinalPopup: false,
      showForm: false,
      showDanmaku: false,
      showTearEmoji: false,
      danmakuItems: [],
      rejectionMessages: [
        '拜托拜托别驳回',
        '再考虑考虑吧',
        '呜呜呜，同意一下呢亲',
        '走嘛~走嘛~',
        '不要这样嘛~',
        '给个机会呗~',
      ],
      currentRejectionMessage: '真的要拒绝我吗？',
      currentMessageIndex: 0,
      acceptMessage: '我已经开始期待了！！！',
      formData: {
        名字: '',
        proof: ''
      },
      rejectButtonStyle: {
        transform: 'scale(1)',
        position: 'relative',
        top: '0px',
        left: '0px',
        opacity: 1
      },
      acceptButtonStyle: {
        transform: 'scale(1)'
      }
    }
  },
  methods: {
    generateDanmaku() {
      const messages = [
        '太棒了！',
        '好开心！',
        '期待见面！',
        '芜湖！！！',
        '太激动了！',
        '终于等到你！',
        '这是最好的选择！',
        '期待！！期待！！',
        '美好的开始！',
        '阿玛特拉斯！',
        '一起出去吧！',
      ];
      
      // 随机选择消息和样式
      const message = messages[Math.floor(Math.random() * messages.length)];
      const variant = `variant-${Math.floor(Math.random() * 4) + 1}`;
      const topPosition = Math.random() * 80 + 10; // 10% - 90%的屏幕高度
      const leftPosition = Math.random() * 100; // 0% - 100%的屏幕宽度

      const moveDuration = 5 + Math.random() * 2; // 5-7秒的动画时长
      
      // 创建弹幕对象
      const danmaku = {
        text: message,
        variant: variant,
        style: {
          '--top-position': `${topPosition}%`,
          '--left-position': `${leftPosition}%`,
          '--move-duration': `${moveDuration}s`
        }
      };
      
      // 添加到弹幕列表
      this.danmakuItems.push(danmaku);
      
      // 等待动画完全结束后再移除弹幕
      // 增加额外的延迟时间，确保弹幕完全移出屏幕后再移除
      // 原来的问题是弹幕可能在完全移出屏幕前就被移除了
      setTimeout(() => {
        const index = this.danmakuItems.indexOf(danmaku);
        if (index > -1) {
          this.danmakuItems.splice(index, 1);
        }
      }, (moveDuration + 0.5) * 1000); // 增加0.5秒的额外延迟
    },
    
    acceptInvitation() {
      // 显示弹幕而不是alert
      this.showDanmaku = true;
      // 隐藏表单界面
      this.showForm = false;
      
      // 立即生成随机弹幕，与中央弹幕同时显示
      for (let i = 0; i < 20; i++) {
        setTimeout(() => this.generateDanmaku(), i * 300);
      }
      
      // 触发烟花效果
      this.triggerFireworks();
    },
    rejectInvitation() {
      this.rejectionCount++;
      this.showRejectionMessage = true;
      
      // 更新拒绝消息，循环显示不同的提示语
      this.currentMessageIndex = (this.currentMessageIndex + 1) % this.rejectionMessages.length;
      this.currentRejectionMessage = this.rejectionMessages[this.currentMessageIndex];
      
      // 交替显示流泪表情
      this.showTearEmoji = !this.showTearEmoji;
      
      // 如果是第7次点击（最后一次），显示最终弹窗
      if (this.rejectionCount === 7) {
        this.showFinalPopup = true;
        return;
      }
      
      // 计算缩放比例和透明度 - 每次点击缩小并变淡
      const scale = Math.max(0.3, 1 - (this.rejectionCount * 0.1));
      const opacity = Math.max(0, 1 - (this.rejectionCount / 7));
      
      // 计算向右下角移动的距离 - 随着点击次数增加而增加
      const moveRight = this.rejectionCount * 15;
      const moveDown = this.rejectionCount * 10;
      
      this.rejectButtonStyle = {
        transform: `scale(${scale})`,
        position: 'relative',
        top: `${moveDown}px`,
        left: `${moveRight}px`,
        opacity: opacity,
        transition: 'all 0.5s ease-in-out'
      };
      
      // 同时让接受按钮变大并向中间移动
      const acceptScale = 1 + (this.rejectionCount * 0.05);
      // 计算向中间移动的距离 - 随着点击次数增加而增加
      const moveToCenter = this.rejectionCount * 10; // 每次点击向中间移动10px
      
      this.acceptButtonStyle = {
        transform: `scale(${acceptScale})`,
        position: 'relative',
        left: `${moveToCenter}px`, // 向中间移动
        transition: 'all 0.5s ease-in-out'
      };
    },
    triggerFireworks() {
      const duration = 5 * 1000;
      const animationEnd = Date.now() + duration;
      const defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 0 };
      
      // 创建从底部升起的烟花
      this.createRisingFireworks();

      function randomInRange(min, max) {
        return Math.random() * (max - min) + min;
      }

      // 原有的爆炸效果保留，但减少频率，让升起的烟花更明显
      const interval = setInterval(() => {
        const timeLeft = animationEnd - Date.now();

        if (timeLeft <= 0) {
          return clearInterval(interval);
        }

        const particleCount = 50 * (timeLeft / duration);
        
        // 随机位置发射烟花
        confetti({
          ...defaults,
          particleCount,
          origin: { x: randomInRange(0.1, 0.3), y: Math.random() - 0.2 }
        });
        confetti({
          ...defaults,
          particleCount,
          origin: { x: randomInRange(0.7, 0.9), y: Math.random() - 0.2 }
        });
      }, 500); // 降低频率
    },
    
    // 创建从底部升起的烟花
    createRisingFireworks() {
      const colors = ['red', 'blue', 'green', 'gold', 'purple'];
      const fireworkCount = 15; // 烟花数量
      
      for (let i = 0; i < fireworkCount; i++) {
        // 延迟创建，使烟花不会同时升起
        setTimeout(() => {
          // 创建烟花元素
          const firework = document.createElement('div');
          firework.className = `firework ${colors[Math.floor(Math.random() * colors.length)]}`;
          
          // 随机位置
          const leftPos = Math.random() * 100;
          firework.style.left = `${leftPos}%`;
          
          // 随机动画持续时间
          const animDuration = 1 + Math.random() * 1.5;
          firework.style.animationDuration = `${animDuration}s`;
          
          // 添加到body
          document.body.appendChild(firework);
          
          // 监听动画结束，在适当高度触发爆炸效果
          firework.addEventListener('animationend', () => {
            // 获取烟花当前位置
            const rect = firework.getBoundingClientRect();
            const x = rect.left / window.innerWidth;
            const y = rect.top / window.innerHeight;
            
            // 在烟花位置触发爆炸
            confetti({
              particleCount: 100,
              startVelocity: 30,
              spread: 360,
              origin: { x, y },
              colors: [getComputedStyle(firework).backgroundColor]
            });
            
            // 移除烟花元素
            document.body.removeChild(firework);
          });
        }, i * 200); // 每隔200ms创建一个烟花
      }
    },
    // 显示证明提交表单
    showProofForm() {
      this.showForm = true;
      this.showFinalPopup = false;
    },
    

    

  }
}
</script>
