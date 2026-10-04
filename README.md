# 卫戍协议：盟约 · 自建联机服务端

复刻 lain42.top 模式：联机走**自家 WebSocket 引擎**（`/ws`），不再依赖境外 PeerJS 公共信令，国内联机稳定。

- 引擎代码：`server/`（2.5M，含 sim/match 全量）
- 游戏数据：`data/`（4.4M，服务端权威模拟用）
- 客户端：**不需要部署在这里**——静态页仍在 GitHub Pages，通过 `?ws=` 直连本服务端
- 本工程总计约 6.8MB，适合免费 PaaS

---

## 一、部署到 Render（免费层，推荐）

1. 把本目录推到一个 GitHub 仓库（如 `yourname/stronghold-server`）：

```bash
cd stronghold-server
git init && git add -A && git commit -m "server"
git remote add origin https://github.com/yourname/stronghold-server.git
git push -u origin main
```

2. 打开 <https://render.com> → New → **Web Service**，连接该仓库。
3. 配置：
   - **Environment**: `Node`
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Instance Type**: Free（够用）
   - 点击 **Create Web Service**
4. 等待部署完成，得到域名 `https://stronghold-server-xxxx.onrender.com`。

> 注意：Render 免费实例**闲置 15 分钟会休眠**，第一次访问会慢几十秒（自动唤醒）；若长期多人联机，建议用 <https://uptimerobot.com> 每 5 分钟 ping 一次保活，或升级付费实例。

## 二、部署到 Railway（备用）

1. 同样把仓库推到 GitHub。
2. <https://railway.com> → New Project → Deploy from GitHub repo → 选择该仓库。
3. Railway 自动识别 `package.json` 的 `start` 脚本（`npm start`），无需额外配置。
4. 部署完成生成域名，形如 `https://<随机名>.up.railway.app`。

> Railway 免费额度 2024 年后已取消，新账号可能要求绑定支付方式——优先用 Render。

## 三、部署后怎么玩（关键）

客户端仍是 GitHub Pages 的静态页，**加两个参数**直连你的服务端：

```
https://xymx1234.github.io/stronghold-standalone/?sp=online&ws=wss://你的域名/ws
```

- 所有人（房主和队友）都用**同一个链接**进入（不要用 `?sp=host`/`?sp=join`，那是 PeerJS 模式）
- 进入后正常创建同盟 / 输入密钥加入——房主和访客都直连你的服务端，**同 WiFi、异网都稳定**
- 服务端重启后游戏内存房间会清空，重新创建同盟即可

## 四、本地自测

```bash
cd stronghold-server
npm install
PORT=3777 node server/index.js
# 另开终端：
curl http://127.0.0.1:3777/data.js        # 200（数据 shim）
# WebSocket 握手：
# 用浏览器打开 http://127.0.0.1:3777 不行（本工程不托管静态页）；
# 联机验证：浏览器打开静态版 + ?ws=ws://127.0.0.1:3777/ws
```

## 五、安全提示

- 服务端开在公网后，任何拿到链接的人都能加入你的房间——只把链接发给熟人即可
- 免费层带宽有限（Render 每月 500MB 出站），几个人玩没问题
