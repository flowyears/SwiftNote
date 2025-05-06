- 注意：解决方案中<font color="#ff0000">“方法1”试过，成功了</font>，其它方法没试过。
---

在安装 Homebrew 时遇到网络问题（如 `curl 18` 和 `git fetch` 错误），通常与**网络连接不稳定**或**国内访问 GitHub 受限**有关。以下是详细解决方案：

---

### **原因分析**
1. **网络中断**：`curl 18` 表示下载过程中连接中断，可能因网络波动或防火墙限制。
2. **Git 仓库拉取失败**：Homebrew 依赖 Git 仓库更新，国内直连 GitHub 可能速度慢或不稳定。
3. **DNS 污染**：部分域名解析被干扰，导致无法正确访问 Homebrew 源。

---

### **解决方案**

#### 方法 1：使用国内镜像加速安装（推荐）
注意：步骤4也需要在步骤3完成后执行。
国内用户可通过更换中科大或清华镜像源解决网络问题：

1. **卸载残留的 Homebrew**（如果有）：
   ```bash
   sudo rm -rf /opt/homebrew
   ```

2. **设置镜像环境变量**：
   ```bash
   # 使用中科大镜像
   export HOMEBREW_API_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles/api"
   export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.ustc.edu.cn/brew.git"
   export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.ustc.edu.cn/homebrew-core.git"
   ```

3. **运行安装脚本**：
   ```bash
   /bin/bash -c "$(curl -fsSL https://mirrors.ustc.edu.cn/misc/brew-install.sh)"
   ```

4. **配置镜像到 Shell 配置文件**：
   ```bash
   # 如果是 Apple Silicon Mac（M1/M2/M3）
   echo 'export HOMEBREW_API_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles/api"' >> ~/.zshrc
   echo 'export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.ustc.edu.cn/brew.git"' >> ~/.zshrc
   echo 'export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.ustc.edu.cn/homebrew-core.git"' >> ~/.zshrc
   source ~/.zshrc
   ```

---

#### 方法 2：手动修复现有安装
如果已开始安装但中途失败：

1. **清理缓存和临时文件**：
   ```bash
   sudo rm -rf /opt/homebrew/Library/Taps/*
   sudo rm -rf $(brew --cache)
   ```

2. **重置 Git 仓库**：
   ```bash
   cd $(brew --repository)
   git reset --hard
   git clean -df
   ```

3. **重试更新**：
   ```bash
   brew update --force --verbose
   ```

---

#### 方法 3：调整 Git 配置（解决大文件超时）
如果错误涉及 Git 拉取超时：
```bash
# 增加 Git 缓冲区大小和超时时间
git config --global http.postBuffer 1048576000  # 1GB 缓冲区
git config --global core.compression 0         # 禁用压缩
```

---

#### 方法 4：使用代理（如有条件）
如果拥有稳定的代理服务：
```bash
# 设置终端代理（替换为你的代理端口）
export ALL_PROXY="http://127.0.0.1:7890"
export HTTPS_PROXY="http://127.0.0.1:7890"
export HTTP_PROXY="http://127.0.0.1:7890"
# 重新运行安装脚本或 brew update
```

---

### **验证安装**
安装完成后检查：
```bash
brew --version
brew doctor
```

---

### **常见问题处理**
- **权限不足**：确保 `/opt/homebrew` 目录归属当前用户：
  ```bash
  sudo chown -R $(whoami) /opt/homebrew
  ```
- **镜像失效**：如果镜像源不可用，切换至清华镜像：
  ```bash
  # 替换镜像源
  export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
  export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
  ```

---

通过以上方法，可解决因网络问题导致的 Homebrew 安装失败。如果问题依旧，建议联系网络管理员或使用企业 VPN 访问国际网络。