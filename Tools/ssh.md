# ssh控制联想拯救者():
## 详细配置步骤

### 阶段一：在 Windows 11 (GPU 主机) 上的配置

#### 步骤 1：安装并配置 WSL2

1. **启用 WSL2：**
    
    - 以管理员身份打开 **PowerShell** 或 **CMD**。
        
    - 运行命令：`wsl --install`（如果您的系统较新，这会安装 WSL 和默认的 Ubuntu 发行版）。
        
    - 按照提示重启计算机。
        
2. **设置 WSL 用户：** 首次启动 Ubuntu 时，系统会提示您设置一个 Linux 用户名和密码。
    
3. **安装必备工具：** 在 WSL 的 Bash 终端中运行：
    
    Bash
    
    ```
    sudo apt update
    sudo apt install -y openssh-server net-tools
    ```
    

#### 步骤 2：配置 Tailscale (内网穿透客户端)

1. **下载和安装：** 在 Windows 11 主机上，从 [Tailscale 官方网站](https://tailscale.com/download/windows) 下载并安装 Tailscale 客户端。
    
2. **登录：** 使用您的账户（推荐 Google, Microsoft, 或 GitHub 账户）登录 Tailscale。
    
3. **获取 IP：** 登录后，Tailscale 会给您的 Windows 主机分配一个特殊的内部 IP 地址（通常是 `100.x.y.z` 格式）。记下这个 IP 地址。
    

#### 步骤 3：配置 WSL2 的 SSH 访问

由于 Windows 11 主机和 WSL2 处于两个不同的网络层，最简单可靠的方式是**通过 Windows 主机 IP 访问 WSL2 的 SSH 服务**。

1. **检查 WSL SSH：** 在 WSL Bash 终端中，确保 SSH 服务正在运行：
    
    Bash
    
    ```
    sudo service ssh start
    # 检查状态
    sudo service ssh status
    ```
    
2. **配置端口转发（从 Win11 到 WSL2）：** 这是一个关键步骤，它将 Windows 11 上的特定端口（例如 `2222`）转发到 WSL2 实例的 22 端口。
    
    - **获取 WSL2 IP：** 在 WSL Bash 终端中运行 `ip addr` 找到 `eth0` 下的 IP 地址（通常是 `172.x.y.z`）。
        
    - **设置端口转发：** 在 **Windows 11 的 PowerShell (管理员权限)** 中运行以下命令（将 IP 和端口替换为您实际的值）：
        
        PowerShell
        
        ```
        # 替换 [WSL2-IP] 为 ip addr 查到的 172.x.y.z 地址
        # 替换 [Windows-Port] 为 2222 (推荐)
        $WSL_IP = "[WSL2-IP]"
        $PORT = 2222
        
        netsh interface portproxy add v4tov4 listenport=$PORT listenaddress=0.0.0.0 connectport=22 connectaddress=$WSL_IP
        
        # 开启防火墙规则 (允许外部 Tailscale IP 访问 2222 端口)
        New-NetFirewallRule -DisplayName "WSL2-SSH-2222" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $PORT
        ```
        
    - **测试：** 在 Windows 11 的 PowerShell 中尝试连接到 WSL2：`ssh -p 2222 [WSL用户名]@127.0.0.1`。如果能成功连接，则端口转发成功。
        

---

### 阶段二：在 Ubuntu (轻薄本) 上的配置

#### 步骤 4：安装 Tailscale 和 SSH 客户端

1. **安装 Tailscale：** 按照 [Tailscale 官方文档](https://tailscale.com/download/linux) 在您的 Ubuntu 电脑上安装 Tailscale。
    
    Bash
    
    ```
    # (Tailscale 官方安装命令，可能随版本变化)
    sudo apt update
    # ... 添加 Tailscale GPG key 和仓库 ...
    sudo apt install tailscale
    ```
    
2. **登录 Tailscale：**
    
    Bash
    
    ```
    sudo tailscale up
    ```
    
    - 按照提示在浏览器中完成登录认证。
        
3. **验证连接：** 确保您的 Ubuntu 电脑获得了 Tailscale IP，并且能 ping 通 Windows 11 的 Tailscale IP (`100.x.y.z`):
    
    Bash
    
    ```
    ping [Windows 11 的 Tailscale IP]
    ```
    

#### 步骤 5：使用 VS Code Remote SSH 连接

1. **安装 VS Code：** 确保您的 Ubuntu 电脑上安装了 VS Code。
    
2. **安装扩展：** 在 VS Code 中安装 **Remote - SSH** 扩展。
    
3. **建立连接：**
    
    - 按下 `Ctrl+Shift+P` (或 `F1`)，输入 `Remote-SSH: Connect to Host...`。
        
    - 选择 `Add New SSH Host...`。
        
    - 输入连接命令，使用 Windows 的 Tailscale IP 和我们在步骤 3 中配置的转发端口：
        
        Bash
        
        ```
        ssh -p 2222 [WSL用户名]@[Windows 11 的 Tailscale IP]
        ```
        
    - 保存配置，然后点击连接。输入您的 WSL 用户密码。


远程连接: ssh -p 2222 bblei_pc@xx (这里ip是由tailscale分配的)






## Server上配置jupyternotebook并且通过远程ssh在local 上通过web访问:

1.在server上运行jupyter notebook

2.在local运行:
ssh -N -f -L port:localhost:port your_username@your_remote_server_ip
这里的port是根据server上运行起来后jupyter提供的port信息来实现的;

3.因为 -N -f -L不会显示并且占用当前shell(后台默默运行);
ps aux | grep "ssh -N -f -L 8889:localhost:8889" | grep -v "grep"  使用该command查找指定ssh进程,记得使用完jupyter 要杀死进程
