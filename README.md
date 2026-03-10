# 🌟 ubuntu-realtime-lowlatency

<br><br>


## Linux Kernel

```bash
# 1
sudo mkdir -p /etc/apt/keyrings
wget -qO - https://dl.xanmod.org/archive.key | sudo gpg --dearmor -vo /etc/apt/keyrings/xanmod-archive-keyring.gpg

# 2
echo "deb [signed-by=/etc/apt/keyrings/xanmod-archive-keyring.gpg] http://deb.xanmod.org $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/xanmod-release.list

# 3
sudo apt install linux-xanmod-lts-x64v3
```


