# lilRay

Для создания аналога xray требуется реализовать набор детекторов.
1) CPU hotspot

### 1. Alloy(в дальнейшем можно заменить на свой bpftrace скрипт) + Pyroscope  + Grafana

https://grafana.com/docs/pyroscope/latest/get-started/
install container image with pyroscope.

for info: https://grafana.com/docs/pyroscope/latest/configure-client/

Настраиваем и запускаем по гайду: https://grafana.com/docs/alloy/latest/set-up/install/linux/

После настройки заходим в grafana http://localhost:3000/connections/datasources
и указываем источник данных: http://pyroscope:4040


Настройка Alloy:
sudo nano /etc/alloy/config.alloy
----------------------------------------------------------------------------------------------------
discovery.process "all" {
}

discovery.relabel "nginx" {
  targets = discovery.process.all.targets

  rule {
    source_labels = ["__meta_process_exe"]
    regex         = ".*/(nginx|openresty)$"
    action        = "keep"
  }

  rule {
    target_label = "service_name"
    replacement  = "nginx"
  }
}

pyroscope.write "local" {
  endpoint {
    url = "http://127.0.0.1:4040"
  }
}

pyroscope.ebpf "nginx" {
  targets    = discovery.relabel.nginx.output
  forward_to = [pyroscope.write.local.receiver]
}
----------------------------------------------------------------------------------------------------



sudo nano /etc/systemd/system/alloy.service.d/override.conf
---------------------------------------------------------------------------------------------------
[Service]
User=root
Group=root
LimitMEMLOCK=infinity
NoNewPrivileges=false
AmbientCapabilities=CAP_SYS_ADMIN CAP_BPF CAP_PERFMON CAP_SYS_PTRACE CAP_DAC_READ_SEARCH CAP_SYS_RESOURCE
CapabilityBoundingSet=CAP_SYS_ADMIN CAP_BPF CAP_PERFMON CAP_SYS_PTRACE CAP_DAC_READ_SEARCH CAP_SYS_RESOURCE
---------------------------------------------------------------------------------------------------

смотрим появились ли изменения: sudo systemctl edit alloy
Перезапускаем: sudo systemctl restart alloy
Смотрим пользака (должен быть root): ps -o user,pid,cmd -C alloy
USER         PID CMD
root       66374 /usr/bin/alloy run --storage.path=/var/lib/alloy/data /etc/alloy/config.alloy


sudo sysctl -w kernel.kptr_restrict=0
sudo sysctl -w kernel.perf_event_paranoid=-1
sudo systemctl restart alloy

Даём нагрузку на nginx и видим графане:
<img width="1850" height="1015" alt="image" src="https://github.com/user-attachments/assets/66002a26-330e-497e-a1d5-538537ddd235" />

<img width="1850" height="1015" alt="image" src="https://github.com/user-attachments/assets/7b6b5b59-5d8d-478c-82a8-36d5b4de0086" />

<img width="1850" height="1015" alt="image" src="https://github.com/user-attachments/assets/aa9e74cf-1503-40fe-ab36-762f8bef3d19" />

