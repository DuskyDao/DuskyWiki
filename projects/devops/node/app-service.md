#linux #nodejs #systemctl 
Запускаем приложение как сервис
```bash
[Unit]
Description=<application-description>
Documentation=https://github.com/test-app
After=network.target

[Service]
Environment=PORT=3000
Type=simple
User=<linux-user>
ExecStart=/usr/bin/node <node-entry-point-js-file-path>
Restart=on-failure

[Install]
WantedBy=multi-user.target
```