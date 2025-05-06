以下是 Git 的常用命令整理，涵盖日常开发中最核心的操作场景：

---

### **一、仓库与基础操作**
| 命令                          | 作用                           | 示例                      |
|-------------------------------|------------------------------|--------------------------|
| `git init`                    | 初始化本地仓库                 | `git init`               |
| `git clone <url>`             | 克隆远程仓库到本地             | `git clone https://github.com/user/repo.git` |
| `git status`                  | 查看工作区与暂存区状态         | `git status -s`（简洁模式） |
| `git add <file>`              | 将文件添加到暂存区             | `git add .`（添加所有文件） |
| `git commit -m "message"`     | 提交暂存区内容到本地仓库        | `git commit -m "feat: add login"` |
| `git log`                     | 查看提交历史                   | `git log --oneline --graph`（简洁图形化） |

---

### **二、分支管理**
| 命令                          | 作用                           | 示例                      |
|-------------------------------|------------------------------|--------------------------|
| `git branch`                  | 查看本地分支                   | `git branch -a`（查看所有分支） |
| `git checkout <branch>`       | 切换分支                       | `git checkout dev`       |
| `git checkout -b <branch>`    | 创建并切换到新分支             | `git checkout -b feature/login` |
| `git merge <branch>`          | 合并指定分支到当前分支          | `git merge dev`          |
| `git rebase <branch>`         | 变基操作（整理提交历史）        | `git rebase main`        |
| `git branch -d <branch>`      | 删除本地分支                   | `git branch -d hotfix`   |

---

### **三、远程仓库操作**
| 命令                          | 作用                           | 示例                      |
|-------------------------------|------------------------------|--------------------------|
| `git remote -v`               | 查看远程仓库地址               | `git remote -v`          |
| `git pull`                    | 拉取远程分支并合并到当前分支     | `git pull origin main`   |
| `git push`                    | 推送本地分支到远程仓库          | `git push origin dev`    |
| `git fetch`                   | 获取远程仓库更新（不自动合并）   | `git fetch --prune`（清理已删除的远程分支） |
| `git push -u origin <branch>` | 推送本地分支并关联远程分支       | `git push -u origin feature/login` |

---

### **四、撤销与回退**
| 命令                          | 作用                           | 示例                      |
|-------------------------------|------------------------------|--------------------------|
| `git restore <file>`          | 撤销工作区未暂存的修改          | `git restore index.html` |
| `git reset <commit>`          | 回退到指定提交（默认保留工作区） | `git reset HEAD~1`（回退到上一提交） |
| `git reset --hard <commit>`   | **强制回退**（丢弃工作区修改）   | `git reset --hard a1b2c3d` |
| `git revert <commit>`         | 生成一个撤销某次提交的新提交     | `git revert a1b2c3d`     |

---

### **五、标签与差异**
| 命令                          | 作用                           | 示例                      |
|-------------------------------|------------------------------|--------------------------|
| `git tag`                     | 查看所有标签                   | `git tag -l "v1.*"`（过滤标签） |
| `git tag <tagname>`           | 创建轻量标签                   | `git tag v1.0.0`         |
| `git tag -a <tagname> -m "msg"` | 创建带注释的标签              | `git tag -a v1.0.0 -m "Release version"` |
| `git diff`                    | 查看工作区与暂存区的差异        | `git diff --staged`（暂存区与仓库差异） |

---

### **六、实用技巧**
1. **修改最后一次提交**：  
   ```bash
   git commit --amend
   ```

2. **暂存当前修改（临时切换分支）**：  
   ```bash
   git stash        # 暂存
   git stash pop    # 恢复
   ```

3. **查看某次提交的改动内容**：  
   ```bash
   git show a1b2c3d
   ```

4. **强制覆盖本地分支为远程状态**：  
   ```bash
   git fetch origin main
   git reset --hard origin/main
   ```

---

### **七、常见问题场景**
- **提交到错误分支**：  
  1. 暂存修改：`git stash`  
  2. 切换到正确分支：`git checkout correct-branch`  
  3. 恢复修改：`git stash pop`  

- **删除远程分支**：  
  ```bash
  git push origin --delete dev
  ```

- **解决合并冲突**：  
  1. 手动编辑冲突文件  
  2. 标记冲突已解决：`git add <file>`  
  3. 完成合并：`git commit`  

---

掌握这些命令后，可覆盖 90% 的日常开发需求。建议配合图形化工具（如 VS Code 的 Git 插件、SourceTree）提升效率！