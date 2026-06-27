/full-pipeline :
授予操作服务端的所有权限。本地所需的所有权限
1：分析项目,优化agent,适配本项目
2：clash网络优化，服务端是否开启了bbr加速？充分利用网络
3：升级脚本内核
4：清理多余的无效文档，整理，让项目清晰可维护。

/full-pipeline : ✅ 已完成
授予操作服务端的所有权限。本地所需的所有权限
1：分析项目 ✅
2：安装流程优化 ✅ — 新增 checkAndEnableBBRAuto() 函数，安装任何选项前自动检测并开启 BBR
3：安装选项2（trojan）后自动装 Trojan-web 面板 ✅ — 新增 installTrojanWebAuto() 函数，无交互安装，显示访问地址和管理方法

