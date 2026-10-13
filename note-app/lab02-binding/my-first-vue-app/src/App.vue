<template>
  <div class="app-container" :class="{ 'dark-mode': isDark }">
    <header class="header">
      <h1>记事本 notes-app</h1>
      <button class="toggle-dark" @click="isDark = !isDark">
        {{ isDark ? '☀️ 切换白天' : '🌙 切换夜间' }}
      </button>
    </header>

    <div class="stats-bar">
      共 {{ notes.length }} 条 | 筛选后 {{ filteredNotes.length }} 条 | 总字数 {{ totalChars }} | 完成率 {{ completionRate }}%
    </div>

    <!-- 1. 我的第一条笔记 -->
    <section class="card">
      <h2>我的第一条笔记</h2>
      <p>hello vue</p >
      <p>HELLO VUE</p >
      <p>{{ count }}</p >
      <p>count = {{ count }} (3 秒后自动变 100 —— 不操作任何 DOM)</p >
      <p>&lt;b&gt;我是 v-text 的纯文本&lt;/b&gt;</p >
      <p v-html="'<b>我是 v-html 解析出的加粗</b>'"></p >
      <p>📷 示例图片</p >
      <p :class="{ highlight: count > 0 }">count > 0 时这行变高亮 (对象语法 :class)</p >
      <div class="btn-group">
        <button @click="count++">count + 1 (内联语句)</button>
        <button @click="say('你好', $event)">say('你好', $event) (方法传参)</button>
      </div>
      <div class="display-area">
        实时展示 → 姓名：{{ form.name }} | 性别：{{ form.gender }} | 爱好：{{ form.hobbies.join(', ') || '暂无' }}
      </div>
      <div class="form-controls">
        <input v-model="form.name" placeholder="输入姓名" />
        <label><input type="radio" value="男" v-model="form.gender" /> 男</label>
        <label><input type="radio" value="女" v-model="form.gender" /> 女</label>
        <label><input type="checkbox" value="篮球" v-model="form.hobbies" /> 篮球</label>
        <label><input type="checkbox" value="编程" v-model="form.hobbies" /> 编程</label>
        <button @click="toggleVisibility">切换显隐 (观察 DevTools 元素面板)</button>
      </div>
      <div class="explain-box">
        <p>v-if: 条件为真才被"渲染"出来</p >
        <p>v-show: 我一直存在，只是被 display:none 藏起来</p >
      </div>
      <ul>
        <li v-for="(sub, index) in subjects" :key="index">{{ index }} - {{ sub }}</li>
      </ul>
      <div class="btn-group">
        <button @click="subjects[1] = '英语'">数组索引赋值: subjects[1] = '英语'</button>
        <button @click="addSubjectProp">对象新增属性: form.school = '...'</button>
      </div>
      <p class="small-text">点我验证 this.$set 已移除</p >
    </section>
    <!-- 2. 组合式写法对照 -->
    <section class="card">
      <h2>组合式写法对照</h2>
      <p>HELLO VUE | count = {{ count }}</p >
      <button @click="count++">count + 1</button>
      <div class="display-area">
        实时展示 → 姓名：{{ form.name }} | 性别：{{ form.gender }} | 爱好：{{ form.hobbies.join(', ') || '暂无' }}
      </div>
      <div class="form-controls">
        <input v-model="form.name" placeholder="输入姓名" />
        <label><input type="radio" value="男" v-model="form.gender" /> 男</label>
        <label><input type="radio" value="女" v-model="form.gender" /> 女</label>
        <label><input type="checkbox" value="篮球" v-model="form.hobbies" /> 篮球</label>
        <label><input type="checkbox" value="编程" v-model="form.hobbies" /> 编程</label>
      </div>
    </section>

    <!-- 3. 记事本 (组件化版) -->
    <section class="card">
      <h2>📝 记事本 (组件化版)</h2>
      <div class="input-group">
        <input v-model="searchQuery" placeholder="搜索笔记..." />
        <input v-model="newNoteTitle" placeholder="输入笔记内容" @keyup.enter="addNote" />
        <button @click="addNote">添加</button>
      </div>
      <div class="radio-group">
        <label><input type="radio" value="高" v-model="newNotePriority" /> 高</label>
        <label><input type="radio" value="中" v-model="newNotePriority" /> 中</label>
        <label><input type="radio" value="低" v-model="newNotePriority" /> 低</label>
      </div>
      <div class="checkbox-group">
        <label><input type="checkbox" value="待办" v-model="newNoteTags" /> 待办</label>
        <label><input type="checkbox" value="灵感" v-model="newNoteTags" /> 灵感</label>
      </div>
      <div class="display-area">
        实时展示 → 内容：{{ newNoteTitle }} | 优先级：{{ newNotePriority }} | 标签：{{ newNoteTags.join(', ') || '无' }}
      </div>
      <p class="small-text green-text">已自动保存 ✔</p >
      <div class="note-list">
        <div v-for="note in filteredNotes" :key="note.id" class="note-item">
          <input type="checkbox" v-model="note.completed" />
          <span :class="{ completed: note.completed }">{{ note.title }}</span>
          <span class="tag">【{{ note.priority }}】</span>
          <span class="tag">{{ note.tags.join(' / ') }}</span>
          <button @click="deleteNote(note.id)">删除</button>
          <span class="small-text">v0.3-notes-app</span>
        </div>
      </div>
      <p class="small-text">显示 {{ filteredNotes.length }} / {{ notes.length }} 条</p >
    </section>
      <!-- 4. 缓存验证 & 侦听器 -->
    <section class="card">
      <h2>📊 缓存验证: computed vs methods (看 Console 打印次数)</h2>
      <p>totalChars 写 3 遍：{{ totalChars }} | {{ totalChars }} | {{ totalChars }}</p >
      <p>getTotalChars() 调 3 遍：{{ getTotalChars() }} | {{ getTotalChars() }} | {{ getTotalChars() }}</p >
      <button @click="unrelatedCount++">触发重渲染 (已点 {{ unrelatedCount }} 次，改的是无关数据)</button>
      <h3 style="margin-top:20px;">⚡ deep / 路径侦听演示 (结果看上方提示条与 Console)</h3>
      <p>user.name = {{ user.name }} | user.city = {{ user.city }}</p >
      <button @click="user.name = '张三'">改 user.name (两种侦听都响)</button>
      <button @click="user.city = '乐山'">改 user.city (只有 deep 响)</button>
    </section>

    <!-- 5. 生命周期 -->
    <section class="card">
      <h2>⏱ 生命周期实验室 (关掉开关 = 销毁组件)</h2>
      <button @click="showLifecycle = !showLifecycle">
        {{ showLifecycle ? '卸载组件 (触发 unmounted)' : '挂载组件 (触发 mounted)' }}
      </button>
      <LifecycleLab v-if="showLifecycle" />
    </section>

    <!-- 6. 事件实验室 -->
    <section class="card">
      <h2>📌 事件实验室 (2.4 事件对象 · 2.5 事件修饰符)</h2>
      <div class="btn-group">
        <button @click="printEvent">打印事件对象 (不传参，首参即事件对象)</button>
        <button @click="showEventArg($event, 66)">showEventArg($event, 66) (传参版)</button>
        <a href=" " target="_blank" @click.prevent="handleLinkClick">Vue 官网链接 (手动 .preventDefault)</a >
      </div>
      <div class="box dashed-box" @click="targetText = '父盒子被点击了'">
        对比盒：先点这段文字，再点下面按钮，看 target 变不变
        <button @click.stop="targetText = '子按钮被点击了'">子按钮 (.stop)</button>
      </div>
      <div class="box outer" @click="log('外层盒子 @click')">
        外层盒子 @click
        <div class="box inner" @click.stop="log('内层 .stop')">内层 .stop</div>
        <div class="box inner" @click="log('内层 无.stop')">内层 无.stop</div>
      </div>
      <p class="small-text">(串联实验) .stop.prevent: 冒泡和跳转都被拦下，只出这一条 ({{ logMessage }})</p >
      <button @click.once="log('抽奖按钮 .once 只能点一次')">抽奖按钮 .once</button>
      <div class="box self-box" @click.self="log('点盒子本身 .self')">
        <p>点盒子本身 (.self)</p >
        <p class="small-text">我是子元素 (点我没用)</p >
      </div>
      <div class="input-group">
        <input @keyup.enter="log('按下了 Enter')" @keyup.esc="log('按下了 Esc')" placeholder="这里按 Enter / Esc 试试" />
      </div>
    </section>
        <!-- ==================== 模块 7：第 2 章实验台 ==================== -->
    <section class="card">
      <h2>ch02-fundamentals | 第 2 章实验台</h2>
      <p class="subtitle">第 03 课上半场 (2.5.3—2.8)：修饰符串联 · 计算属性 · 侦听器 · 样式绑定</p >
      
      <div class="exp-box">
        <h3>实验 01 | 修饰符串联 (2.5.3)</h3>
        <p>串联按从左到右依次执行：`.stop` 先剪断冒泡，`.prevent` 再拦下跳转。</p >
        <div class="box outer" @click="log('外层盒子 @click')">
          外层盒子 @click
          <div class="box inner" @click.stop="log('内层 .stop')">内层 .stop</div>
          <div class="box inner" @click="log('内层 无.stop')">内层 无.stop</div>
        </div>
        <p class="small-text">串联 .stop.prevent: 冒泡和跳转都被拦下，只出这一条 ({{ logMessage }})</p >
      </div>

      <div class="exp-box">
        <h3>实验 02 | 计算属性 (2.6)</h3>
        <p>搜索过滤 + 统计 + 缓存验证 (打开 F12 Console 看打印次数)。</p >
        <div class="stats-bar">共 {{ notes.length }} 条 | 筛选后 {{ filteredNotes.length }} 条 | 总字数 {{ totalChars }} | 完成率 {{ completionRate }}%</div>
        <div class="note-list">
          <div v-for="note in filteredNotes" :key="note.id" class="note-item">
            <input type="checkbox" v-model="note.completed" />
            <span :class="{ completed: note.completed }">{{ note.title }}</span>
            <span class="tag">【{{ note.priority }}】</span>
          </div>
        </div>
        <p class="small-text">缓存验证: computed 依赖不变时不重新计算，methods 每次渲染都执行</p >
      </div>

      <div class="exp-box">
        <h3>实验 03 | 侦听器 (2.7)</h3>
        <p>勾选/增删触发自动保存；改 name 两种侦听都响，改 city 只有 deep 响。</p >
        <div class="deep-demo">
          <p>user.name = {{ user.name }} | user.city = {{ user.city }}</p >
          <button @click="user.name = '张三'">改 user.name (两种侦听都响)</button>
          <button @click="user.city = '乐山'">改 user.city (只有 deep 响)</button>
        </div>
      </div>

      <div class="exp-box">
        <h3>实验 04 | 样式绑定 (2.8)</h3>
        <p>① 对象语法: 条件挂类</p >
        <p :class="{ 'text-green-bold': count > 0 }">count > 0 时这行变绿加粗 (count = {{ count }})</p >
        <button @click="count++">count + 1</button>
        <p>② 数组语法: 三选一切换</p >
        <button @click="cycleSize">循环切换 S→M→L (当前: {{ size }})</button>
        <p>③ 数组嵌对象: 固定类 + 条件类 (最常用)</p >
        <button :class="['btn-fixed', { 'btn-active': isActive }]" @click="isActive = !isActive">切换完成态</button>
        <p>:style 对象绑定: 色板滑块</p >
        <div class="color-slider">
          <label>颜色 <input type="color" v-model="themeColor" /></label>
          <label>字号 <input type="range" min="12" max="30" v-model="fontSize" /></label>
          <span :style="{ color: themeColor, fontSize: fontSize + 'px' }">颜色 {{ themeColor }} | 字号 {{ fontSize }}px</span>
        </div>
      </div>
    </section>
    <!-- ==================== 模块 8：第 3 章实验台 ==================== -->
    <section class="card">
      <h2>ch03-components | 第 3 章实验台</h2>
      <p class="subtitle">第 03 课下半场 (3.1—3.7)：两种 API · 生命周期 · 注册 · 样式冲突 · props / emit / inject</p >

      <div class="exp-box">
        <h3>实验 01 | 选项式 API vs 组合式 API (3.1)</h3>
        <p>教材例 3-1 / 例 3-2 原文对照：同一个计数器，两种写法。</p >
        <div class="props-row">
          <div class="box" style="flex:1;"><h4>选项式 API</h4><p>计数器的值为：2</p ><button>+1</button></div>
          <div class="box" style="flex:1;"><h4>组合式 API</h4><p>计数器的值为：1</p ><button>+1</button></div>
        </div>
      </div>

      <div class="exp-box">
        <h3>实验 02 | 生命周期 (3.2)</h3>
        <p>挂载 → 点 +1 (updated) → 卸载 (unmounted, Console 看清理)。子组件的 mounted 先于父组件。</p >
        <button @click="showLifecycle = !showLifecycle">
          {{ showLifecycle ? '卸载 LifecycleLab (触发 unmounted)' : '挂载 LifecycleLab (created → mounted)' }}
        </button>
        <LifecycleLab v-if="showLifecycle" />
      </div>

      <div class="exp-box">
        <h3>实验 03 | 组件注册 (3.3)</h3>
        <p>左：局部注册 (App 的 components 选项) | 右：全局注册 (main.js 的 app.component，App 没有 import)</p >
        <div class="props-row" style="margin-top: 15px;">
          <div class="box" style="flex: 1; border: 1px solid #64b5f6; background: #f0f8ff;">
            <h4>局部注册</h4>
            <p>我在 App.vue 的 <strong>components</strong> 选项里注册，只有 App 能用我。</p >
            <LocalComponentDemo /> 
          </div>
          <div class="box" style="flex: 1; border: 1px dashed #81c784; background: #e8f5e9;">
            <h4>全局注册</h4>
            <p>我在 <strong>main.js</strong> 里注册 (app.component)，App 没 import 我也能用——但代价是始终会打进包里。</p >
            <GlobalComponentDemo />
          </div>
        </div>
      </div>

      <div class="exp-box">
        <h3>实验 04 | 样式冲突与 scoped (3.4)</h3>
        <p>F12 检查下面的盒子：标签上带 data-v-xxxx 哈希属性——scoped 给选择器"加锁"。</p >
        <div class="box green-box">父组件自己的 .box (绿色)</div>
        <ScopedChild />
        <p class="small-text">:deep() 穿透：下面这行文字的红色，是父组件 scoped 样式伸进子组件改的。</p >
        <div class="box" style="border: 1px solid red; color: red; background: #fff;">子组件 ScopedChild 的 .box (蓝色——没被父组件的绿色样式染到)</div>
      </div>

      <div class="exp-box">
        <h3>实验 05 | props 父传子 (3.5)</h3>
        <p>PriceBox 用对象方式声明 props (type/required/default)；点按钮故意传错类型，看 Console 警告。</p >
        <div class="props-row">
          <PriceBox title="手机价格" :price="3999" :count="2" />
          <PriceBox title="手机价格" :price="3999" :count="'abc'" />
        </div>
        <p class="small-text">当前 count = 2 (正常 Number) | badCount = abc (故意传错，看 Console)</p >
        <div class="btn-group">
          <button @click="unrelatedCount++">count + 1</button>
          <button @click="unrelatedCount++">换一个错误类型</button>
        </div>
        <p class="small-text">⚠️ 单向数据流：子组件不许改 props (改了报 target is readonly)，要改就 emit 通知父组件 (见实验 06)。</p >
      </div>

      <div class="exp-box">
        <h3>实验 06 | emit 子传父 (3.6)</h3>
        <p>记忆模型：父 —props(只读)— 子；父 —@监听— emit(报信) — 子。</p >
        <p>父组件收到的消息：<span class="highlight">{{ childMessage }}</span></p >
        <SendCard @send-msg="handleChildMsg" />
      </div>

      <div class="exp-box">
        <h3>实验 07 | provide / inject 跨级 (3.7)</h3>
        <p>爷爷 (本组件) provide 一辆车；中间层 MiddleChild 零传递；孙子 GrandChild 直接 inject。</p >
        <p>爷爷说：我提供的是「{{ carName }}」</p >
        <button @click="carName = '奔驰'">换一辆车 (注意：选项式 provide 非响应式，孙子不会跟变)</button>
        <MiddleChild />
      </div>
    </section>

  </div>
</template>

<script setup>
import { ref, reactive, computed, watch, onMounted, onUnmounted, provide, defineComponent } from 'vue';
// ================== 全局状态 ==================
const isDark = ref(false);
const count = ref(0);
const searchQuery = ref('');
const form = reactive({ name: '张三', gender: '男', hobbies: ['篮球'] });
const user = reactive({ name: '张三', city: '成都' });
const subjects = ref(['语文', '数学', 'Vue']);

// 样式绑定
const size = ref('M');
const isActive = ref(false);
const themeColor = ref('#42b883');
const fontSize = ref(18);

const cycleSize = () => {
  if (size.value === 'S') size.value = 'M';
  else if (size.value === 'M') size.value = 'L';
  else size.value = 'S';
};

// 记事本
const notes = ref([
  { id: 1, title: '学习单文件组件三段结构', priority: '高', tags: ['待办'], completed: true },
  { id: 2, title: 'v-model 表单三件套', priority: '中', tags: ['待办', '灵感'], completed: false },
  { id: 3, title: '给记事本加上删除功能', priority: '低', tags: ['灵感'], completed: false }
]);
const newNoteTitle = ref('');
const newNotePriority = ref('低');
const newNoteTags = ref(['灵感']);
const unrelatedCount = ref(0);
const logMessage = ref('');
const showLifecycle = ref(false);
const childMessage = ref('');
const carName = ref('宝马');

const addNote = () => {
  if (!newNoteTitle.value.trim()) return;
  notes.value.push({ id: Date.now(), title: newNoteTitle.value, priority: newNotePriority.value, tags: [...newNoteTags.value], completed: false });
  newNoteTitle.value = '';
};
const deleteNote = (id) => { notes.value = notes.value.filter(n => n.id !== id); };

const filteredNotes = computed(() => {
  if (!searchQuery.value) return notes.value;
  return notes.value.filter(n => n.title.includes(searchQuery.value) || n.priority.includes(searchQuery.value));
});
const totalChars = computed(() => notes.value.reduce((sum, n) => sum + n.title.length, 0));
const getTotalChars = () => notes.value.reduce((sum, n) => sum + n.title.length, 0);
const completionRate = computed(() => {
  if (notes.value.length === 0) return 0;
  const done = notes.value.filter(n => n.completed).length;
  return Math.round((done / notes.value.length) * 100);
});

const handleChildMsg = (msg) => { childMessage.value = msg; };
const log = (msg) => { console.log(msg); logMessage.value = msg; alert(msg); };
const printEvent = (e) => { console.log('事件对象:', e); alert('事件对象已打印到 Console'); };
const showEventArg = (e, num) => { console.log(e, num); alert(`传参成功: ${num}`); };
const handleLinkClick = () => { alert('链接跳转被阻止'); };
const say = (text, e) => { console.log(e); alert(text); };
const toggleVisibility = () => {};
const addSubjectProp = () => {};

watch(user, () => console.log('watch user (浅层) 触发了'));
watch(user, () => console.log('watch user (deep) 触发了'), { deep: true });
onMounted(() => {
  setTimeout(() => { count.value = 100; }, 3000);
});
provide('car', carName);
// ================== 子组件定义 ==================
const LifecycleLab = defineComponent({
  name: 'LifecycleLab',
  setup() {
    const labCount = ref(0);
    onMounted(() => console.log('1. created: 组件创建'));
    onMounted(() => console.log('2. mounted: DOM 就绪'));
    onUnmounted(() => console.log('3. unmounted: 组件卸载'));
    return { labCount };
  },
  template: `<div class="sub-component"><p>我是 LifecycleLab 组件</p ><p>count = {{ labCount }} (点 +1 触发 updated，关掉开关触发 unmounted)</p ><button @click="labCount++">+1</button></div>`
});

const LocalComponentDemo = defineComponent({ template: `<div style="font-size:12px; color:#666;">[局部组件] 我只能在 App 里被使用</div>` });
const GlobalComponentDemo = defineComponent({ template: `<div style="font-size:12px; color:#666;">[全局组件] 其实我在 main.js 注册，这里模拟一下</div>` });
const ScopedChild = defineComponent({ template: `<div class="box blue-box">子组件 ScopedChild 的 .box (蓝色——没被父组件的绿色样式染到)</div>` });

const PriceBox = defineComponent({
  props: { title: String, price: Number, count: { type: Number, required: true, default: 1 } },
  setup(props) {
    const total = computed(() => props.price * props.count);
    return { total };
  },
  template: `<div class="price-box"><h4>{{ title }}</h4><p>手机价格：{{ price }}，购买数量：{{ count }}，总计：{{ total }}</p ><p v-if="isNaN(total)" class="error-text">当前 count 传入了非数字类型，计算结果为 NaN</p ></div>`
});

const SendCard = defineComponent({
  emits: ['send-msg'],
  setup(props, { emit }) {
    return { sendFixed: () => emit('send-msg', 'Hello from 子组件!'), sendData: () => emit('send-msg', '这是子组件发送的数据') };
  },
  template: `<div class="sub-component"><p>我是子组件 SendCard</p ><button @click="sendData">发送数据给父组件</button><button @click="sendFixed">发送固定问候</button></div>`
});

const MiddleChild = defineComponent({
  template: `<div class="middle-child"><p>我是中间层 MiddleChild: 我没有传递任何数据下去。</p ><GrandChild /></div>`
});

const GrandChild = defineComponent({
  inject: ['car'],
  template: `<div class="grand-child"><p>我是孙子 GrandChild: 爷爷传来的车 = {{ car }} (inject 直达，没经过中间层)</p ><p class="small-text">⚠️ 选项式 provide 的值不是响应式 (创建时定值) —— 这是教程 3.7 的进阶边界，组合式传 ref 可保持响应式。</p ></div>`
});
</script>
<style>
* { box-sizing: border-box; }
body { margin: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; }
.app-container { max-width: 900px; margin: 0 auto; padding: 20px; background-color: #f8f9fa; color: #333; transition: all 0.3s; }
.app-container.dark-mode { background-color: #1a1a1a; color: #e0e0e0; }
.app-container.dark-mode .card, .app-container.dark-mode .exp-box { background-color: #2d2d2d; border-color: #444; }
.app-container.dark-mode input, .app-container.dark-mode select { background-color: #444; color: white; border-color: #666; }
.header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
.header h1 { margin: 0; font-size: 24px; }
.toggle-dark { padding: 6px 12px; border-radius: 20px; border: 1px solid #ccc; cursor: pointer; }
.stats-bar { font-size: 13px; color: #666; margin-bottom: 20px; padding: 8px; background: #eef1f5; border-radius: 4px; }
.dark-mode .stats-bar { background: #333; color: #aaa; }
.card, .exp-box { background: #fff; border-radius: 8px; padding: 20px; margin-bottom: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.05); border: 1px solid #eee; transition: all 0.3s; }
h2 { font-size: 18px; border-bottom: 2px solid #42b883; padding-bottom: 8px; margin-top: 0; }
h3 { font-size: 16px; color: #42b883; margin-top: 0; }
.subtitle { font-size: 13px; color: #888; margin-top: -10px; margin-bottom: 15px; }
button { padding: 6px 12px; border: 1px solid #42b883; background: #fff; color: #42b883; border-radius: 20px; cursor: pointer; font-size: 13px; transition: all 0.2s; margin-right: 8px; margin-bottom: 8px; }
button:hover { background: #42b883; color: #fff; }
input[type="text"], input[type="search"], input:not([type]) { padding: 6px 10px; border: 1px solid #ddd; border-radius: 4px; outline: none; margin-right: 8px; }
.input-group { display: flex; gap: 10px; margin-bottom: 10px; flex-wrap: wrap; }
.radio-group, .checkbox-group { margin-bottom: 10px; font-size: 14px; }
.radio-group label, .checkbox-group label { margin-right: 15px; }
.display-area { background: #f0fdf4; border: 1px dashed #42b883; padding: 10px; margin: 10px 0; border-radius: 4px; }
.dark-mode .display-area { background: #1e3a2f; }
.green-text { color: #42b883; font-weight: bold; }
.note-list { margin-top: 15px; }
.note-item { display: flex; align-items: center; gap: 10px; padding: 10px 0; border-bottom: 1px solid #eee; }
.note-item.completed span { text-decoration: line-through; color: #aaa; }
.tag { font-size: 12px; padding: 2px 8px; background: #f0f0f0; border-radius: 4px; color: #666; }
.dark-mode .tag { background: #444; color: #ccc; }
.box { padding: 15px; border: 2px solid #ccc; margin: 10px 0; border-radius: 4px; cursor: pointer; }
.outer { background: #fff3e0; border-color: #ffb74d; }
.inner { background: #e3f2fd; border-color: #64b5f6; margin-left: 20px; }
.dashed-box { border-style: dashed; border-color: #ffb74d; background: #fff3e0; }
.self-box { background: #e0f2f1; border: 1px solid #80cbc4; }
.self-box .small-text { pointer-events: none; }
.sub-component { background: #f0f8ff; padding: 15px; border: 1px solid #b0d4ff; border-radius: 6px; margin-top: 10px; }
.small-text { font-size: 12px; color: #999; }
.highlight { background: #e8f5e9; padding: 2px 6px; border-radius: 4px; border-left: 3px solid #42b883; }
.text-green-bold { color: #4caf50; font-weight: bold; }
.btn-fixed { border-color: #2196f3; color: #2196f3; }
.btn-active { background: #2196f3; color: #fff; }
.color-slider { display: flex; flex-direction: column; gap: 10px; margin-top: 10px; }
.green-box { background: #e8f5e9; border-color: #81c784; }
.blue-box { background: #e3f2fd; border-color: #64b5f6; }
.props-row { display: flex; gap: 20px; flex-wrap: wrap; }
.price-box { flex: 1; min-width: 250px; background: #fffde7; padding: 15px; border-radius: 6px; border: 1px solid #fff59d; }
.error-text { color: #d32f2f; font-weight: bold; }
.middle-child { background: #f5f5f5; padding: 15px; border: 1px dashed #ccc; border-radius: 6px; margin-top: 10px; }
.grand-child { background: #e0f2f1; padding: 15px; border: 1px solid #80cbc4; border-radius: 6px; margin-top: 10px; }
</style>
