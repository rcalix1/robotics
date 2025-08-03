## AI hardware

* Jetson orin nano
* https://www.youtube.com/watch?v=q4fGac-nrTI
* 

## Perceptron in hardware

* https://www.youtube.com/watch?v=l-9ALe3U-Fg&t=41s
* https://maker.pro/forums/threads/analogue-perceptron.298609/
* https://github.com/rcalix1/MachineLearningFoundations/tree/main/NeuralNets/perceptron
* https://isl.stanford.edu/~widrow/papers/j2005thinkingabout.pdf
* 10k resistors
* 10k pots everywhere
* 

# 🧠 Jetson Orin Nano — Persistent LLM Setup (Open WebUI + Ollama)

This guide sets up **Open WebUI** and **Ollama** on a Jetson Orin Nano so both persist across reboots. WebUI runs automatically on boot, and Ollama can be optionally auto-started.

---

## ✅ 1. Run Open WebUI as a Persistent Docker Container

Run this **once** to set up Open WebUI with:

* Background execution
* Auto-restart on boot
* Persistent volume for chat history and settings

```bash
docker run -d \
  --name open-webui \
  --restart always \
  --network=host \
  --add-host=host.docker.internal:host-gateway \
  -v openwebui_data:/app/backend/data \
  ghcr.io/open-webui/open-webui:main
```

After reboot, Open WebUI will be available at:

```
http://<orin-nano-ip>:3000
```

To stop or restart:

```bash
docker stop open-webui
docker start open-webui
```

---

## ✅ 2. Optional: Auto-Start Ollama on Boot

If you don’t want to SSH in each time to start `ollama serve`, you can set up a `systemd` service.

### Create the service file:

```bash
sudo nano /etc/systemd/system/ollama.service
```

### Paste this:

```ini
[Unit]
Description=Ollama LLM Server
After=network.target

[Service]
Type=simple
User=rcalix1
ExecStart=/usr/bin/ollama serve
Restart=always

[Install]
WantedBy=multi-user.target
```

> Replace `rcalix1` with your actual username if different.
> Confirm Ollama's binary is located at `/usr/bin/ollama` (use `which ollama` to verify).

### Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable ollama
sudo systemctl start ollama
```

### To check status:

```bash
systemctl status ollama
```

---

## ✅ Done!

* Open WebUI runs automatically on boot
* Ollama can now auto-start with system boot if desired
* Models are stored on SSD (via symbolic link)
* System is stable and reboot-proof
