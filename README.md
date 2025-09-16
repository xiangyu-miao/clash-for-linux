# Clash for Linux

A simple guide to set up and use Clash, a rule-based proxy utility, on Linux systems (without UI).

## Installation

1. Clone this repository to your local machine:

```bash
git clone https://github.com/xiangyu-miao/clash-for-linux.git  # HTTPs
```
```bash
git clone git@github.com:xiangyu-miao/clash-for-linux.git  # SSH
```
```bash
cd clash-for-linux
```

2. Download and prepare the Clash binary:

```bash
wget https://github.com/Kuingsmile/clash-core/releases/download/1.18/clash-linux-amd64-v1.18.0.gz
```

3. Unzip the downloaded file:

```bash
gunzip clash-linux-amd64-v1.18.0.gz
```

4. Make the binary executable:

```bash
chmod +x clash-linux-amd64-v1.18.0
```
5. Rename the binary for easier access (optional):

```bash
mv clash-linux-amd64-v1.18.0 clash
```

## Proxy Setup

To use Clash as a proxy, you need to set the following environment variables in your terminal:

```bash
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
```

To disable the proxy, you can unset these variables:

```bash
unset http_proxy
unset https_proxy
```

For convenience, you can add the following commands to your shell profile (e.g., `~/.bashrc` or `~/.zshrc`).

```bash
alias proxy_on="export http_proxy=http://127.0.0.1:7890;export https_proxy=http://127.0.0.1:7890;"
alias proxy_off="unset http_proxy;unset https_proxy;"
```

And then source your profile:

```bash
source ~/.bashrc  # or source ~/.zshrc
```

Then you can easily toggle the proxy on and off using `proxy_on` and `proxy_off` commands in your terminal.

```bash
proxy_on  # Enable proxy
env | grep -i proxy  # Verify proxy settings
proxy_off  # Disable proxy
```

## Configuration

Clash requires a configuration file named `config.yaml` to operate. Here is a basic example of what the `config.yaml` file might look like:

```yaml
port: 7890
socks-port: 7891
allow-lan: false
mode: Rule
log-level: info
external-controller: 127.0.0.1:9090 # Optional: for GUI control
proxies:
  - name: "ExampleProxy"
    type: http
    server: example.com # Replace with your proxy server
    port: 8080
    username: user # Optional
    password: pass # Optional
proxy-groups:
  - name: "Auto"
    type: select
    proxies:    
      - "ExampleProxy"
      - "DIRECT"
rules:
  - DOMAIN-SUFFIX,example.com,Auto
  - GEOIP,CN,DIRECT
  - MATCH,Auto
```

You should prepare your own `config.yaml` file based on your proxy provider's details and your routing preferences. Then place it in the `conf` directory in this repository.

## Running and Stopping Clash

To start Clash with your configuration file, run the following command in the terminal:

```bash
./clash -d ./configs &> ./logs/clash.log &      
```

This command runs Clash in the background, using the configuration file located in the `configs` directory and logging output to `logs/clash.log`.

If you want to stop Clash (for example, when you want to update the configuration or when you no longer need the proxy), you can find its process ID:

```bash
ps -ef | grep -v grep | grep clash
```

Then kill the process using its PID:

```bash
kill <PID>
```

Replace `<PID>` with the actual process ID of the Clash process.

## Setting Up Clash as a systemd Service (Optional)

This will allow Clash to start automatically on boot and be managed like any other Linux service.

1. Create a systemd service file for Clash:

```bash
sudo nano /etc/systemd/system/clash.service
```

2. Add the following content to the file, adjusting paths as necessary:

```ini
[Unit]
Description=Clash Service

[Service]
Type=simple
ExecStart=/path/to/clash -d /path/to/configs
```

Make sure to replace `/path/to/clash` and `/path/to/configs` with the actual paths on your system.

3. Reload systemd to recognize the new service:

```bash
sudo systemctl daemon-reload
```

4. Enable the Clash service to start on boot:

```bash
sudo systemctl enable clash
```

5. Start the Clash service:

```bash
sudo systemctl start clash
```

6. Check the status of the Clash service:

```bash
sudo systemctl status clash
```

7. To stop the Clash service, use:

```bash
sudo systemctl stop clash
```

## Contributing

If you find this project helpful, please consider giving it a star on GitHub!

## Disclaimer

This project is intended for educational and personal use only. The author does not condone or encourage the use of this software to violate any laws or regulations. Users are solely responsible for how they choose to use Clash and must ensure compliance with local laws and network policies. The author is not liable for any misuse, data loss, or legal consequences resulting from the use of this project.
