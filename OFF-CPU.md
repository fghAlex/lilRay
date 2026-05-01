### Второй детектор позволяющий выявить проблемы производительности.

* Одна из возможных интепретаций результатов:
latency высокий
  + CPU hotspot низкий (детектор 1)
  + off-CPU вырос (детектор 2)
  = wait-bound проблема


#### План
Для mvp возьмем готовый скрипт 1. offcputime.py из https://github.com/iovisor/bcc#tracing
  Так же нужен 2. wrapper/agent
  3. Prometheus metrics
  4. Loki events
  5. Grafana dashboard

в Grafana будут собираться метрики:
offcpu_ratio
offcpu_seconds_total
top offcpu stacks
slow/offcpu events
