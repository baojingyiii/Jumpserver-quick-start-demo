# Jumpserver-quick-start-demo
开源堡垒机JumpServer学习

## 🤔 组件
| Component  | Description |
|------------|-------------|
| Core       | JumpServer Core 相当于后端|
| Lina       | JumpServer Web UI 相当于前端|
| Luna       | JumpServer Web Terminal（终端） |
| KoKo       | JumpServer Character Protocol Connector（字符协议连接器）ssh连接器 |
| Lion       | JumpServer Graphical Protocol Connector（图形协议连接器）Windows远程桌面 |
| Chen       | JumpServer Web DB（数据库） |
| Tinker     | JumpServer Remote Application Connector (Windows)（Windows应用程序连接器） |
| Panda      | JumpServer EE Remote Application Connector (Linux)（Linux应用程序连接器） |
| Razor      | JumpServer EE RDP Proxy Connector（RDP代理连接器） |
| Magnus     | JumpServer EE Database Proxy Connector（数据库代理连接器） |
| Nec        | JumpServer EE VNC Proxy Connector（VNC代理连接器） |
| Facelive   | JumpServer EE Facial Recognition（面部识别） |

```
1. Core - 核心后端 ✅ 必须
就是JumpServer的主程序，提供API接口、用户认证、权限管理

2. Nginx - Web服务器 ✅ 必须
代理所有前端请求，统一入口
接收用户请求 → 转发给Lina(Web界面)或Core(API)

3. Lina - Web管理界面 ✅ 必须
就是你在浏览器看到的那个管理页面
点击按钮、填写表单的地方
通过Nginx访问，不直接暴露端口

4. Luna - Web终端界面 ✅ 必须
就是那个在浏览器里打开的SSH终端窗口
点"连接资产"后弹出的黑色终端
也是通过Nginx访问

5. Koko - SSH/Telnet连接器 ✅ 必须
专门处理SSH/Telnet连接
当SSH连接服务器时，实际连接的是Koko，它再连到目标服务器

6. Lion - RDP/VNC连接器 ✅ 可选（如果需要图形连接）
专门处理Windows远程桌面(RDP)和VNC连接
如果要远程Windows服务器，就需要这个
纯Linux环境可以不用
```
