```
SF80007775MACJA:~ 80007775$ gem install cocoapods-art ERROR: While executing gem ... (Gem::FilePermissionError) You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
```

以下是解决 **`gem install` 权限问题** 的完整方案：

---

### 🔍 **错误原因**
你试图将 gem（如 `cocoapods-art`）安装到 macOS 系统自带的 Ruby 路径 `/Library/Ruby/Gems/2.6.0`，而普通用户默认没有该目录的写入权限。直接使用 `sudo` 安装会导致后续权限混乱，因此 **不推荐**。

---

### 🌟 **推荐方案：通过 Homebrew 管理独立 Ruby 环境**
#### **1. 安装/更新 Homebrew**
```bash
# 确保 Homebrew 最新
brew update && brew upgrade
```

#### **2. 安装新版 Ruby**
```bash
# 通过 Homebrew 安装最新 Ruby
brew install ruby

# 配置环境变量（添加到 ~/.zshrc 或 ~/.bash_profile）
echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc
echo 'export PATH="/usr/local/lib/ruby/gems/$(ruby -v | awk "{print \$2}" | cut -d. -f1,2)/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

#### **3. 验证 Ruby 路径**
```bash
which ruby  # 应输出 /usr/local/opt/ruby/bin/ruby
ruby -v     # 应显示 3.x 版本（非系统自带的 2.6.x）
```

#### **4. 安装 cocoapods-art（无权限问题）**
```bash
# 国内用户建议换源加速
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

# 安装插件
gem install cocoapods-art
```

---

### ⚠️ **如果仍需使用系统 Ruby**
#### **1. 安装到用户目录（推荐）**
```bash
gem install cocoapods-art --user-install
```

#### **2. 添加用户 gem 路径到环境变量**
```bash
echo 'export GEM_HOME="$HOME/.gem"' >> ~/.zshrc
source ~/.zshrc
```

---

### 🔧 **验证安装**
```bash
# 检查插件是否可见
gem list cocoapods-art

# 检查 CocoaPods 是否能识别插件
pod repo-art list
```

---

### 📝 **补充说明**
• **为何避免使用 `sudo gem install`**：  
  系统 Ruby 目录受 macOS 保护，强制写入可能破坏系统完整性，且更新 macOS 时会重置该目录。
• **Homebrew Ruby 的优势**：  
  完全独立于系统环境，避免权限问题，适合长期开发。

---

遇到其他问题，请提供以下信息进一步排查：
1. 运行 `gem env` 的输出。  
2. 运行 `echo $PATH` 的输出。