Proyecto: Balanceador de Carga con HAProxy + Apache + Datadog

Este proyecto implementa un sistema de balanceo de carga utilizando **HAProxy** como balanceador principal y **Apache** en tres servidores backend. Además, cada servidor reporta su estado y métricas a **Datadog** para monitoreo en tiempo real.

---

 Estructura del proyecto



balanceador/
├── haproxy.cfg
├── datadog-agent instalado

servidor1/
├── Apache instalado
├── index.html personalizado
├── datadog-agent instalado

servidor2/
├── Apache instalado
├── index.html personalizado
├── datadog-agent instalado

servidor3/
├── Apache instalado
├── index.html personalizado
├── datadog-agent instalado


---

 Requisitos

- 4 máquinas (físicas o virtuales) con Ubuntu (recomendado Ubuntu 20.04 o superior)
- Acceso a Internet para instalar paquetes
- Una cuenta en [Datadog](https://www.datadoghq.com/)
- Clave de API de Datadog

---

 Paso 1: Configurar los servidores Apache

### En cada servidor (servidor1, servidor2, servidor3):

1. Instala Apache:
   ```bash
   sudo apt update
   sudo apt install apache2 -y

Verifica que funcione:

echo "<h1>Servidor 1</h1>" | sudo tee /var/www/html/index.html


 Paso 2: Instalar HAProxy en el balanceador

sudo apt update
sudo apt install haproxy -y
sudo nano /etc/haproxy/haproxy.cfg

global
    log /dev/log local0
    maxconn 2048
    daemon

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend http_front
    bind *:80
    default_backend http_back

backend http_back
    balance roundrobin
    server servidor1 192.168.3.101:80 check
    server servidor2 192.168.3.102:80 check
    server servidor3 192.168.3.103:80 check

Reinicia HAProxy:
sudo systemctl restart haproxy

Prueba accediendo desde el navegador al IP del balanceador:
http://192.168.3.104


Paso 3: Configurar Datadog en cada servidor

Descarga e instala el agente de Datadog en cada servidor y el balanceador:
DD_API_KEY="TU_API_KEY_AQUI" bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script.sh)"

Verifica que el agente esté funcionando:
sudo datadog-agent status

(Opcional) Habilita la integración de Apache editando:
sudo nano /etc/datadog-agent/conf.d/apache.d/conf.yaml

init_config:

instances:
  - apache_status_url: http://localhost/server-status?auto

Reinicia el agente:
sudo systemctl restart datadog-agent

 Verificación
 ingresa desde cualquier cliente con:
curl 192.168.3.104:8084


