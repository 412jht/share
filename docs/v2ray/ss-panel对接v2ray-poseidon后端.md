# **ss-panel 对接 v2ray-poseidon后端**
1. ## 安装
       curl -L -s https://raw.githubusercontent.com/ColetteContreras/v2ray-poseidon/master/install-release.sh | sudo bash

2. ## 配置
  - v2ray配置
    - 修改
           vi /etc/v2ray/config.json

  - TSL配置
    - Let's Encrypt证书生成
      - 安装acme.sh:
             curl https://get.acme.sh | sh

        安装成功后执行:
             source ~/.bashrc

        如果缺少`netcat`:
             sudo apt-get -y install netcat

      - 生成证书:
             sudo ~/.acme.sh/acme.sh --issue -d 域名 --standalone -k ec-256

        如果使用的是cloudflare进行域名解析，可以调用api进行域名验证：
        - 开启API：https://dash.cloudflare.com/profile/api-tokens
        - 设置API变量：  
               export CF_Key="配置好的API"  
               export CF_Email="用户名邮箱"`
        - 生成证书：  
               sudo ~/.acme.sh/acme.sh --issue --dns dns_cf -d 域名 --standalone -k ec-256

      - 证书更新：
      由于 Let's Encrypt 的证书有效期只有 3 个月，因此需要 90 天至少要更新一次证书，acme.sh 脚本会每 60 天自动更新证书。也可以手动更新。

        手动更新 ECC 证书，执行：  
             sudo ~/.acme.sh/acme.sh --renew -d mydomain.com --force --ecc
      - 安装证书：
      将 ECC 证书和密钥安装到 /etc/v2ray 中：  
             sudo ~/.acme.sh/acme.sh --installcert -d 域名 --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key --ecc

  - ss-panel配置：
    1. 新增节点；
    2. 编辑节点：  
    `节点名称` `自定义`  
    `节点地址` `域名;端口;ws;4;tls;host;路径`  
    `端口类型` `只启用普通端口`  
    `节点类型` `V2Ray`  
    保存修改

  - WebSocket-TLS 模式:
  [详细配置](https://raw.githubusercontent.com/412jht/share/s/docs/v2ray/v2ray-config.json)
3. ## 操作
       service v2ray start   #启动
       service v2ray stop    #停止
       service v2ray restart #重启


4. ## 日志
       journalctl -u v2ray #查看所有日志

       systemctl status v2ray #查看当前日志

5. ## 备注
      支持限制ip功能(在ss-panel服务端运行)：
       sed -i "s/'u', 'd')/'u', 'd', 'node_connector')/" $(find ./ -iname 'UserController.php' | grep Mod_Mu)

6. ## 参考
  [对接 SSPanel 教程](https://github.com/ColetteContreras/v2ray-poseidon/wiki/0202-%E5%AF%B9%E6%8E%A5-SSPanel-%E6%95%99%E7%A8%8B)  
  [TLS配置](https://toutyrater.github.io/advanced/tls.html)  
  [acme签发证书](https://painso.com/2017/02/28/acme-lets-encrypt-issue/)
