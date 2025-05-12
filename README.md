

![image](https://github.com/user-attachments/assets/c912adba-b9b2-431f-acd5-99f6f6ce6753)

 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>
 * [Discord](https://discord.com/invite/0glabs)<br>


### UBUNTU 24 İSTİYOR. 22 OLANLAR DOCKERLI KURULUMU 22 DE YAPABİLİR.

### Gereklilikler ve ayarlar
```
sudo apt update && sudo apt upgrade && sudo apt install wget rar zip libssl-dev ca-certificates -y
```
```
sudo ufw allow 443/tcp
sudo ufw allow 80/tcp
```

```
wget https://download.pipe.network/static/pop-v0.3.0-linux-x64.tar.gz
sudo tar -xzf pop-v0.3.0-linux-*.tar.gz -C /usr/local/bin
sudo chmod +x /usr/local/bin/pop
pop --help
```
```
sudo mkdir -p $HOME/pipe
sudo mkdir -p $HOME/pipe/logs
cd $HOME/pipe
```
```
sudo chmod 755 $(which pop)
sudo chown -R $USER:$USER $HOME/pipe
```
```
curl -o config.json https://raw.githubusercontent.com/Core-Node-Team/PIPE-Tesnet/refs/heads/main/config.json
```
```
nano config.json
```
NOT: cüzdan pubkey adınız bilgileriniz gibi alt kısımdaki şeyleri değiştirin.

### Servis oluşturalım
```
sudo tee /etc/systemd/system/popd.service > /dev/null << EOF
[Unit]
Description=POP Cache Node
After=network.target

[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/pipe
ExecStart=$(which pop)
Restart=always
RestartSec=5
LimitNOFILE=65535
StandardOutput=append:$HOME/pipe/logs/stdout.log
StandardError=append:$HOME/pipe/logs/stderr.log
Environment=POP_CONFIG_PATH=$HOME/pipe/config.json

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl restart popd
sudo systemctl enable popd
```
```
sudo systemctl status popd
```
# Monitor application logs
```
tail -f $HOME/pipe/logs/stderr.log
```
```
tail -f $HOME/pipe/logs/stdout.log
```
# Monitor system service logs
```
sudo journalctl -u popd -fo cat
```
### Log rotation
```
sudo nano /etc/logrotate.d/popd
```
```
/root/pipe/logs/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 root root
    sharedscripts
    postrotate
        systemctl reload popd >/dev/null 2>&1 || true
    endscript
}
```
### yararlı komutlar
```
curl http://localhost/state
```
### Restart
```
sudo systemctl restart popd
```
#### Check metrics
```
curl http://localhost/metrics
```
#### Check the health endpoint
```
curl http://localhost/health
```
#### LOG
```
sudo journalctl -u popd -n 100
```
#### Validate your configuration
```
pop --validate-config 
```


