---
created: 2025-09-14 19:17
modified: 2026-01-24 22:37
---
# vscode
## 配置
- setting的优先级【工作目录】>【服务器】>【本地】
- [vscode去除菜单栏白边](https://blog.csdn.net/m0_64140451/article/details/126647601)
- [修改VSCode默认安装路径](https://blog.csdn.net/qq_37435462/article/details/128809760)
- 字体：Consolas, 'Courier New', monospace
- 设置：Python：Conda Path：`/root/host_share/miniconda3/_conda `有时python插件无法自动检测到conda路径手动设置
- `workbenche nable preview`：打开文件的行为是永久打开还是预览
## 命令
1. code -r xx/：强制在已打开的窗口中打开文件或文件夹
2. code -n xx/：强制打开一个新窗口

## 快捷键
1. <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>：命令面板
2. <kbd>Ctrl</kbd> + <kbd>`</kbd>：打开终端
3. <kbd>Ctrl</kbd> + <kbd>/？</kbd>：注释
4. <kbd>Ctrl</kbd> + <kbd>+-</kbd>：界面缩放
5. <kbd>Ctrl</kbd> + <kbd>k</kbd>，<kbd>Ctrl</kbd> + <kbd>数字</kbd>：折叠到某一级的代码块，如<kbd>Ctrl</kbd> + <kbd>k</kbd>，<kbd>Ctrl</kbd> + <kbd>0</kbd>会折叠所有代码块
6. <kbd>Ctrl</kbd> + <kbd>k</kbd>，<kbd>Ctrl</kbd> + <kbd>-</kbd>：折叠除了选中外的所有代码块
7. <kbd>Ctrl</kbd> + <kbd>k</kbd>，<kbd>Ctrl</kbd> + <kbd>J</kbd>：展开所有代码块
8. <kbd>Ctrl</kbd> + <kbd>k</kbd>，<kbd>Ctrl</kbd> + <kbd>W</kbd>：关闭所有打开的文件
9. ctrl + shift + v：对md文件，预览模式模式间切换
10. <kbd>alt</kbd> + <kbd>f</kbd>：打开大纲（focus on outline view）
11. <kbd>Ctrl</kbd> + <kbd>-</kbd>：减小字体，<kbd>Ctrl</kbd> + <kbd>=</kbd>：增大字体
## 插件
- Dev Containers：用于连接本地的docker容器
- romote ssh：ssh远程连接插件[手动安装 vscode-server](https://zhuanlan.zhihu.com/p/699761292)
- [github copilot](https://vscode.js.cn/docs/copilot/ai-powered-suggestions)：代码补全
e3550cfac4b63ca4eafca7b601f0d2885817fd1f