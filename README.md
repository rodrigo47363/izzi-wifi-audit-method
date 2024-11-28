# Método de Auditoría WiFi para Routers Izzi

Este repositorio documenta un método específico para analizar contraseñas predeterminadas de routers Izzi, enfocado en auditorías de seguridad en entornos controlados.

> ⚠️ **Advertencia Ética**: Este método está diseñado exclusivamente para fines educativos. **No utilices este conocimiento de forma no autorizada.**  

## 📋 Descripción del Método
1. Uso de `crunch` para generar listas de contraseñas basadas en patrones conocidos.
2. Uso de `aircrack-ng` para probar contraseñas generadas contra un archivo de captura (`.cap`).

Este método puede optimizarse si se conoce parte de la contraseña, como:
- Longitud fija de 12 caracteres.
- Uso de letras mayúsculas y números.
- Relación con la BSSID del router.

## 🌟 Características
- **Ejemplos prácticos** de comandos para casos con y sin patrones conocidos.
- **Lista de caracteres recomendada** para contraseñas predeterminadas de Izzi.
- **Documentación detallada** para guiarte en el proceso.

## 🛠️ Requisitos
1. **Sistema Operativo**: Linux (preferiblemente Kali Linux).
2. **Herramientas Necesarias**:
   - `crunch`
   - `aircrack-ng`

## 🚀 Ejemplo Rápido
### Conociendo Parte de la Contraseña
```bash
sudo crunch 12 12 -t F82DC0@@C5DC -f Diccionario.lst anu | aircrack-ng --bssid 00:11:22:33:44:55 -w- IZZI-C5DC.cap
```

### Sin Conocer Nada de la Contraseña
```bash
sudo crunch 10 12 -f Diccionario.lst anu | aircrack-ng --bssid 00:11:22:33:44:55 -w- IZZI-0652.cap
```

Consulta la carpeta `docs/` para guías más detalladas.

## 📁 Estructura del Repositorio
```plaintext
izzi-wifi-audit-method/
├── README.md
├── LICENSE
├── docs/
│   ├── overview.md
│   ├── setup-guide.md
│   ├── using-crunch.md
│   ├── using-aircrack-ng.md
│   └── stopping-process.md
├── examples/
│   ├── example-command-with-pattern.sh
│   ├── example-command-no-pattern.sh
│   └── Diccionario.lst
├── captures/
│   ├── example.cap
│   └── README.md
```

## ✨ Contribuciones
Las contribuciones son bienvenidas, pero deben alinearse con el objetivo educativo del repositorio. Abre un _issue_ para sugerencias o mejoras.

## 📄 Licencia
Este proyecto se distribuye bajo la licencia [MIT](LICENSE).

## ⚠️ Advertencia Final
El uso indebido de esta información puede tener consecuencias legales. Este proyecto está enfocado únicamente en el aprendizaje y pruebas éticas de seguridad.

---

© 2024 - Rodrigo | [LinkedIn](https://linkedin.com/in/rodrigo-v-695728215) | [GitHub](https://github.com/rodrigo47363)
```
