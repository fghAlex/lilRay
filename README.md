# lilRay

Для создания аналога xray требуется реализовать набор детекторов.
1) CPU hotspot

### 1. Pyroscope

https://grafana.com/docs/pyroscope/latest/get-started/
install container image with pyroscope.

for info: https://grafana.com/docs/pyroscope/latest/configure-client/

Настраиваем и запускаем по гайду: https://grafana.com/docs/alloy/latest/set-up/install/linux/

После настройки заходим в grafana http://localhost:3000/connections/datasources
и указываем источник данных: http://pyroscope:4040


Настройка Alloy:
sudo nano /etc/alloy/config.alloy
sudo nano /etc/systemd/system/alloy.service.d/override.conf
alex@alex-Specified:~/src/nginx-1.26.3/nginx-tests$ sudo systemctl edit alloy
alex@alex-Specified:~/src/nginx-1.26.3/nginx-tests$ sudo systemctl restart alloy
alex@alex-Specified:~/src/nginx-1.26.3/nginx-tests$ ps -o user,pid,cmd -C alloy
USER         PID CMD
root       66374 /usr/bin/alloy run --storage.path=/var/lib/alloy/data /etc/alloy/config.alloy


sudo sysctl -w kernel.kptr_restrict=0
sudo sysctl -w kernel.perf_event_paranoid=-1
<img width="1850" height="1015" alt="image" src="https://github.com/user-attachments/assets/66002a26-330e-497e-a1d5-538537ddd235" />
