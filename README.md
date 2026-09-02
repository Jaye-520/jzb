# ⭐ 星星人记账工作台

纯前端单页记账应用（手机竖屏优先）。零后端、零依赖、零网络请求：双击 `index.html` 即可使用，也可部署到任意静态托管（GitHub Pages / Nginx / 对象存储 / 内网服务器）。

## 交付文件

| 文件 | 说明 |
|---|---|
| `index.html` | 完整应用（内联 CSS/JS，两张参考图均已 base64 内嵌，约 700KB，可直接打开） |
| `assets/source-bg.jpg` | 全页背景源图（884×1920，参考图副本，仅留档用） |
| `assets/source-grid.jpg` | 星星人九宫格源图（1320×1734，运行时从内嵌副本裁切头像，仅留档用） |
| `README.md` | 本说明 |

## 部署方式（任选其一）

1. **直接打开**：浏览器双击 `index.html`（file:// 协议完全可用）。
2. **本地预览**：`python -m http.server 8000` 后访问 `http://127.0.0.1:8000`。
3. **静态托管**：把 `index.html` 放到任意静态服务器 / GitHub Pages 根目录即可（无需构建）。

### 部署到 Vercel

项目已内置 `vercel.json`（静态输出，不构建）与 `.vercelignore`（忽略 assets/）。

**方式 A：Git 仓库导入（推荐，可持续更新）**

1. 把本目录推到 GitHub：
   ```powershell
   cd C:\Users\Jaye\Documents\project\TTY3
   git init
   git add .
   git commit -m "星星人记账工作台 v1.0"
   git remote add origin https://github.com/<你的用户名>/<仓库名>.git
   git push -u origin main
   ```
2. 打开 [vercel.com](https://vercel.com) → Add New → Project → 导入该仓库。
3. Framework Preset 选 **Other**，Build Command 留空，Output Directory 留空（或填 `.`），点 Deploy。
4. 部署完成后每次 `git push` 自动重新发布。

**方式 B：Vercel CLI（最快，无需 Git）**

```powershell
cd C:\Users\Jaye\Documents\project\TTY3
npx vercel        # 首次会引导登录（浏览器授权）
npx vercel --prod # 正式发布，得到 https://<项目名>.vercel.app
```

部署后说明：

- 站点是单文件 + hash 路由（`#m=xxx`），Vercel 无需任何 rewrites 配置，刷新/直达子模块都正常。
- 数据仍存访问者各自浏览器的 localStorage；**你手机上记的账不会出现在别人设备上**，跨设备迁移用「设置 → 导出/导入」。
- 如需自定义域名：Vercel 项目 → Settings → Domains 添加即可。
- `assets/` 与 `README.md` 不会随部署上传（`.vercelignore`），`index.html` 自包含全部资源。

## 功能一览

- **17 个模块**（左侧常驻毛玻璃侧边栏，手机 72px / 平板 150px / 桌面 200px，永不收起）：
  概览、明细、代办清单、统计、报表、预算、账户、**理财**（位于账户与借贷之间）、借贷、目标、模板、周期、账本、日历、减脂、经期助手、设置
- **模块 Banner**：左侧圆形头像（从星星人九宫格运行时裁切 9 张 512×512，白底 contain，按模块循环分配、相邻不重复）；右侧标题+副标题；8 个 emoji 以 4×2 网格随机抖动散布（旋转 ±11°、17–22px、透明度 0.3–0.5），每次切换模块重新随机
- **全页背景**：参考图 base64 内嵌 `cover` 铺满 + 奶油色遮罩
- **账本隔离（绝对核心）**：所有实体（明细/账户/预算/借贷/目标/模板/周期/代办/减脂/经期）均带 `bookId`；顶部「📚 当前账本」按钮弹出账本列表，当前账本高亮，点击即切换即关闭，全局数据即时联动
- **代办清单**：每日固定（跨日自动复位）+ 临时，支持增/勾/编/删
- **统计**：左侧分类柱状图 + 右侧 6 卡片（居住/购物/学习/餐饮/美妆/宠物）
- **报表**：2×2 四卡片（收入/支出/结余/储蓄率=结余÷收入；正绿负红；无收入显示 0%）
- **理财**：两列 6 卡片网格，canvas 自生成 16:11 实拍风封面（暖金/冷蓝/绿/深蓝红/多彩/粉），分类标签 + 中央 ▶ + 「查看行情」+「复制链接」；链接指向 gold.eastmoney.com / quote.eastmoney.com/center / fund123.cn / boc.cn/sourcedb/whpj
- **减脂**：身高体重档案 + BMI/BMR/TDEE 计算 + 热量/运动/饮食/体重录入 + 体重趋势折线
- **经期助手**：周期档案 + 生理期/黄体期/排卵期记录 + 预测（下次经期/排卵日/黄体期）+ 月历着色（记录实色、预测浅色）
- **账户/借贷/目标/预算/模板/周期** 均支持编辑与删除
- **设置**：导出 JSON 备份 / 导入恢复 / 清空数据

## 数据说明

- 所有数据保存在浏览器 `localStorage`（键名 `tt3-workbench-v1`），**不经过任何服务器**。
- 换浏览器、清理站点数据、使用无痕模式都会导致数据不可见，请定期在「设置」中导出备份。
- 切换账本后各模块数据即时联动；删除账本会连带删除其名下全部数据（有确认提示）。

## 关键实现

- 头像：运行时常驻 9 宫格 JPEG（base64 内嵌）+ canvas 按实测矩形裁切 → 512×512 白底 contain 图（9 张，内存缓存），模块 i 使用第 i%9 张。
- 理财封面：canvas 程序化绘制（渐变 + 景深模糊 + 胶片噪点 + 暗角 + 主题元素：金条金币 / 银锭 / 蜡烛图K线 / 货币符号 / 礼盒星光），生成后缓存为 dataURL。
- 响应式断点：<768px 72px 栏 / 768–1023px 150px 栏 / ≥1024px 200px 栏；内容区最大 1040px。
- 无构建步骤、无外部字体/CDN；兼容现代 Chrome / Edge / Safari / Firefox 及移动端 WebView。

v1.0
