# Deploy Angular SSR with bun.js + GO API Rest in AWS EC2 & AWS Amplify

## Deploy GO Application in EC2 and expose API with NGROK
### Prerequisites
- GO (https://go.dev/doc/install)
- GIT (https://git-scm.com/download/linux)
- ngrok (https://ngrok.com/download)

### 1. Generate build
```bash
go build main.go
```

### 2. Configuration EC2
#### Cloning repository
```bash
git clone https://github.com/username/repository
```

#### Installling dependencies
```bash
go mod tidy
```

#### Generating binary file
```bash
go build main.go
```

#### Move binary file to services folder
```bash
sudo mv main /srv/my-application
```

#### Configure service to run application in background using systemd
```bash
cd /etc/systemd/system
```

```bash
vim my-application.service
```

```vim
[Unit]
Description=My Application
After=network.target

[Service]
ExecStart=/srv/my-application
Restart=always
User=ubuntu
WorkingDirectory=/srv

[Install]
WantedBy=multi-user.target
```

#### Restart services
```bash
sudo systemctl daemon-reload
```

#### Enable service
```bash
sudo systemctl enable myapp
```

#### Show service status
```bash
sudo systemctl status myapp
```

#### Show logs API
```bash
sudo journalctl -u myapp
```

#### Expose API using NGROK in background
```bash
sudo bash -c 'nohup ngrok http 3000 &> /var/log/ngrok.log &'
```

#### Package.json
```json
{
    "scripts": {
        "ng": "ng",
        "start": "bun x ng serve --port 4500",
        "build:prod": "bun x ng build --configuration production",
        "build:dev": "bun x ng build --configuration development",
        "watch": "ng build --watch --configuration development",
        "test": "ng test"
    }
}
```

#### Show ngrok tunnels
```bash
curl http://127.0.0.1:4040/api/tunnels
```

## Deploy Angular App
### 1. Configure AWS Amplify
```yaml
version: 1
frontend:
    phases:
        preBuild:
            commands:
                - 'curl -fsSL https://bun.sh/install | bash'
                - 'source /root/.bashrc'
                - 'bun install'
        build:
            commands:
                - 'bun run build:dev'
    artifacts:
        baseDirectory: dist/my-application/browser
        files:
            - '**/*'
    cache:
        paths:
            - 'node_modules/**/*'
```
`baseDirectory: your dist directory`