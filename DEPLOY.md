# របៀប Deploy Telegram Bot ទៅ VPS តាម Termius

## ១. តម្រូវការ
- VPS (Ubuntu 22.04 ឬ 24.04 ត្រូវបានណែនាំ)
- Termius សម្រាប់ SSH ចូល VPS
- Telegram Bot Token (ពី @BotFather)

## ២. ភ្ជាប់ទៅ VPS តាម Termius
1. បើក Termius → New Host
2. បំពេញ IP, Username (ច្រើនតែ `root` ឬ `ubuntu`), Password / SSH Key
3. ចុច Connect

## ៣. ដំឡើង Python និង Git នៅលើ VPS
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y python3 python3-pip python3-venv git
```

## ៤. យកកូដចូល VPS
### វិធីទី១៖ Clone ពី GitHub
```bash
cd ~
git clone https://github.com/<your-username>/<your-repo>.git telegram-bot
cd telegram-bot
```

### វិធីទី២៖ Upload ផ្ទាល់តាម Termius (SFTP)
- បើក Termius → SFTP → ភ្ជាប់ host
- ផ្ទុកឯកសារទាំងអស់ទៅ `/home/ubuntu/telegram-bot/`

## ៥. បង្កើត Virtual Environment និងដំឡើង Dependencies
```bash
cd ~/telegram-bot
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
pip install python-dotenv
```

## ៦. កំណត់ Environment Variable
```bash
cp .env.example .env
nano .env
```
បំពេញ Token របស់អ្នក៖
```
TELEGRAM_BOT_TOKEN=123456:ABC-YourTokenHere
```
រក្សាទុក (Ctrl+O, Enter, Ctrl+X)។

## ៧. សាកល្បងដំណើរការ Bot
```bash
source venv/bin/activate
python bot.py
```
បើដំណើរការល្អ ចុច `Ctrl+C` ដើម្បីបញ្ឈប់ រួចបន្តទៅជំហានបន្ទាប់។

## ៨. ដំឡើងជា systemd Service (ដើម្បីដំណើរការ ២៤/៧ និង auto-restart)
```bash
sudo cp telegram-bot.service /etc/systemd/system/telegram-bot.service
```

> បើ user ឬ path របស់អ្នកខុសពី `ubuntu` និង `/home/ubuntu/telegram-bot` សូមកែ file នេះមុន៖
> ```bash
> sudo nano /etc/systemd/system/telegram-bot.service
> ```

ដំណើរការ service៖
```bash
sudo systemctl daemon-reload
sudo systemctl enable telegram-bot
sudo systemctl start telegram-bot
sudo systemctl status telegram-bot
```

## ៩. មើល Log
```bash
sudo journalctl -u telegram-bot -f
# ឬ
tail -f /var/log/telegram-bot.log
```

## ១០. បញ្ឈប់ / Restart
```bash
sudo systemctl stop telegram-bot
sudo systemctl restart telegram-bot
```

## ១១. Update កូដថ្មី
```bash
cd ~/telegram-bot
git pull
sudo systemctl restart telegram-bot
```

រួចរាល់! Bot របស់អ្នកនឹងដំណើរការនៅលើ VPS ២៤/៧។
