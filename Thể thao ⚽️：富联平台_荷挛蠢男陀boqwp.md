富联平台【Q-——333307——】富联平台【 辋芷《888yx●vip》 】
富联平台【Q-——333307——】富联平台【 辋芷《888yx●vip》 】

 从0到1，我用Python写了个自动化部署工具（附完整代码）

> 写脚本2小时，部署到服务器又折腾了一下午？这痛点我太懂了。今天分享一个我最近用Python写的自动化部署小工具，思路清晰，代码全公开，希望能帮你省下那些“无效加班”的时间。

 为什么你需要一个部署脚本？

手动部署的流程通常是：本地打包 → 上传服务器 → SSH登录 → 备份旧包 → 替换 → 重启服务。步骤多且容易出错，尤其是“手滑”覆盖了配置文件的时候，心态直接爆炸。

自动化部署的本质，就是把固定的操作步骤代码化，让机器去执行，人只负责触发。 这不仅减少了人为失误，更是团队协作中提效的关键一环。

 工具核心思路拆解

这个脚本我尽量做得简单通用，核心就三步：

1.  本地构建：自动执行 `npm run build` 或 `mvn package`；
2.  远程推送：通过 `paramiko` 库实现 SFTP 上传，支持增量覆盖；
3.  远程执行：在服务器上执行 `restart.sh`，并自动备份旧版本。

用到的Python库只有两个：`paramiko`（处理SSH连接）和 `scp`（或者直接用SFTP）。

 关键代码逻辑（仅展示核心片段）

```python
import paramiko
import os

def deploy(server_ip, username, password, local_path, remote_path):
     1. 建立SSH连接
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(server_ip, username=username, password=password)
    sftp = ssh.open_sftp()
    
     2. 备份远端旧文件
    ssh.exec_command(f"cp -r {remote_path} {remote_path}_bak")
    
     3. 上传新文件
    sftp.put(local_path, remote_path)
    
     4. 重启服务（具体命令视项目而定）
    stdin, stdout, stderr = ssh.exec_command("bash /opt/scripts/restart.sh")
    print(stdout.read().decode())
    
    sftp.close()
    ssh.close()
    print("✅ 部署完成")
```

注意：生产环境不要用密码登录，建议改成密钥对认证，脚本逻辑不变，只是换掉 `connect` 参数。

 运行效果与避坑指南

执行 `python deploy.py` 后，终端会打印出重启服务的日志。如果遇到“Connection refused”或者“Permission denied”，大概率是服务器防火墙没开端口，或者运行用户没有该目录的写权限。

避坑tips：
- 先手动跑一遍 `restart.sh`，确认能单独执行；
- 上传前务必做好备份，别省这一步；
- 如果项目依赖了环境变量，记得在远端 `~/.bashrc` 里配置好。

 下一步还能怎么玩？

这个脚本目前是“手动触发”的，你可以很轻松地把它接入 GitHub Actions 或者 Jenkins。只要在代码推送后自动执行这个Python脚本，就能实现真正的 CI/CD 持续部署。

---

互动一下：你现在部署上线一套代码大概需要多久？有没有遇到过什么离谱的部署事故？欢迎在评论区吐槽，我准备了小礼物送给点赞最高的朋友。

如果这篇文章对你有帮助，点个赞和在看，让更多被部署折磨的兄弟看到。后续我会更新接入GitHub Actions的具体教程，关注我不迷路。

相关推荐：

https://github.com/stewartpamela7264/qbqsmb/blob/main/Th%E1%BB%83%20thao%20%E2%9A%BD%EF%B8%8F%EF%BC%9A%E5%AF%8C%E8%81%94%E6%B3%A8%E5%86%8C%E6%B3%A8%E5%86%8C_%E6%96%9C%E6%B3%8A%E6%83%A8%E8%97%95%E6%82%B8dgurb.md

<img src="https://i.postimg.cc/Z5BSNcSn/fulian-00001.png" />

相关推荐：

https://github.com/stewartpamela7264/qbqsmb/commit/7010e44f4ca990c089bdac00a4b2a675f866bf3c

<img src="https://i.postimg.cc/KzNSbpnw/fulian-00009.png" />
相关推荐：

https://github.com/greenecaitlin50/mngkhu/blob/main/Tr%E1%BB%B1c%20tuy%E1%BA%BFn%20%F0%9F%90%93%EF%BC%9A%E5%AF%8C%E8%81%94%E6%B3%A8%E5%86%8C%E5%BC%80%E6%88%B7_%E6%89%8D%E9%A9%B6%E5%88%97%E5%A4%9C%E7%82%8Almvet.md

<img src="https://i.postimg.cc/503dJKBd/fulian-00011.png" />
相关推荐：

https://github.com/greenecaitlin50/mngkhu/commit/b446fd5d4d809b5326582ed4cad69821f68eb55f

<img src="https://i.postimg.cc/C1NT0rjt/fulian-00008.png" />

资讯来源：新华网、人民网、央视新闻、中新网、凤凰网、澎湃新闻、界面新闻、新浪新闻、搜狐网、财新网、观察者网、第一财经等主流平台，以独树一帜的观察视角与扎实的深度报道能力，在资讯领域收获广泛关注。
