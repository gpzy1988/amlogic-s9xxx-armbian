# TYC4A（天翼云RK3568笔记本）Armbian 一键云端编译

## 为什么直接用 z96a 板型？
经过硬件比对，TYC4A 与 朝歌Z96A 云笔记本使用完全相同的PCB和引脚定义：
- CPU: RK3568
- PMIC: RK817
- 网卡: RTL8211F 千兆RGMII
- WiFi/BT: RTL8821CS
- 屏幕: BOE NV140FHM-N48 eDP
- 背光: PWM10
- 充电: CN3705
- USB HUB: FE1.1s

**唯一区别是 Z96A 用 DDR4、TYC4A 用 LPDDR4X**，但 DDR 初始化在 U-Boot 阶段完成，
内核 DTB 不区分 DDR 类型，所以直接用 Z96a 的固件 100% 可用。

## 操作步骤（约5分钟，编译等30-60分钟）

1. 打开 https://github.com/ophub/amlogic-s9xxx-armbian 点右上角 Fork 到你账号
2. 进入你 Fork 后的仓库（https://github.com/你的用户名/amlogic-s9xxx-armbian）
3. 在仓库页面依次点：Add file → Upload files
4. 把 build-tyc4a.yml 上传到 .github/workflows/ 目录下
   （如果 .github/workflows/ 目录不存在，直接上传文件时在输入框里写 `.github/workflows/build-tyc4a.yml`）
5. 页面往下滑点 Commit changes 保存
6. 点仓库上方 Settings → 左侧 Actions → General
   - 找到 Workflow permissions，选 Read and write permissions
   - 勾选 Allow GitHub Actions to create and approve pull requests
   - 点 Save
7. 点仓库上方 Actions 标签页，左侧列表找到 "Build TYC4A Armbian (5.10 kernel)"
8. 点右侧 Run workflow 按钮，弹出下拉框保持默认（bookworm + xfce）
9. 点绿色 Run workflow 开始编译

## 下载固件
编译完成后（约30-60分钟）：
1. 点进那个成功的编译任务
2. 页面最底部 Artifacts 区域，下载 Armbian_bookworm_xfce_TYC4A-rk3568_5.10.img.gz

## 刷写和启动
- 用 balenaEtcher 或 Rufus 把 .img.gz 写入 ≥16GB 的 SD 卡
- 插卡开机，默认会从SD卡启动
- 默认账号：root / 1234（首次登录强制改密码）
- 启动到系统后执行 `armbian-install` 可写入内置eMMC
- 串口调试：UART2 @ 1500000 波特率

## 已包含的驱动（unifreq 5.10.y-rk35xx 内核）
- RTL8821CS WiFi + 蓝牙
- Mali G52 GPU (Panfrost)
- MPP 硬件编解码 + RGA 2D加速
- NPU 驱动 (rknn)
- RK817 声卡
- RTL8211F 千兆网卡
- USB 3.0 / Type-C 供电/数据
- eDP + HDMI 双显示
- 电池电量上报
- 合盖休眠（霍尔传感器）
