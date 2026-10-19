# Arquitectura del laboratorio

Objetivo: un entorno reproducible donde se ejercitan las materias prácticas
(SO I y II, Telecom, Programación Segura, Ciberseguridad aplicada, IoT,
Forense, Criptografía), separado del entorno de trabajo diario.

## Principio

**Todo lo que se rompe, se rompe en una VM.** El host es un lugar de trabajo, no
un campo de pruebas. Nada de instalar herramientas ofensivas en el sistema base.

## Capas

```text
HOST  Ubuntu 26.04 LTS  ── trabajo diario, Pi, Docker, Coach
 │
 ├── volumen cifrado LUKS (externo USB-C, o 2do M.2 si existe)
 │    └── /lab
 │         ├── imagenes/      qcow2 de cada VM
 │         ├── plantillas/    qcow2 base reutilizables
 │         ├── forense/       imágenes y evidencia de práctica
 │         └── datasets/      datasets chicos de ML/practica
 │
 └── libvirt
      ├── red lab-aislada    192.168.100.0/24, sin salida a internet
      ├── red lab-servicios  192.168.101.0/24, NAT para actualizar
      └── bridge fisico      sobre I219-LM para segmentos reales
```

## Set mínimo de máquinas (arranque)

| VM | Rol | vCPU | RAM | Disco | Cuándo |
| --- | --- | --- | --- | --- | --- |
| `deb-lab` | Servidor Linux de práctica (SSH, servicios, systemd) | 2 | 4 GB | 20 GB | Año 1, SO I |
| `win-eval` | Windows 11/Server eval: AD, GPO, forense de Windows | 2 | 6 GB | 40 GB | Año 1–2 |
| `kali-lab` | Herramientas de análisis y pentesting | 2 | 4 GB | 25 GB | Año 2, Ciberseguridad aplicada |
| `remnux` | Análisis de malware | 2 | 4 GB | 20 GB | Año 3, Forense |
| `iot-lab` | Emulación de firmware / dispositivos | 1 | 2 GB | 10 GB | Año 2, IoT |

Total con las cinco encendidas: 10 vCPU, 20 GB RAM, 115 GB. La máquina medida
(12 hilos, 30 GiB) lo soporta; el disco no, y por eso el laboratorio va sobre
almacenamiento dedicado. En la práctica no se encienden todas a la vez: 2–3
simultáneas es el uso normal.

## Reglas de operación

1. **Plantillas antes que instancias.** Cada SO tiene una imagen base
   (`plantilla-deb.qcow2`) que se clona con `qemu-img create -f qcow2 -b`.
   Nunca se instala dos veces el mismo sistema.
2. **Snapshots antes de cada práctica destructiva.** `virsh snapshot-create-as`
   es más rápido que reinstalar.
3. **La red aislada no tiene salida.** Si una práctica necesita descargar algo,
   se baja en el host y se sirve localmente. Una VM comprometida no debe poder
   llamar a su casa.
4. **Las imágenes forenses son de solo lectura.** Se trabaja sobre copias, nunca
   sobre el original, y se documenta el hash (`sha256sum`) antes de tocarlas.
5. **El laboratorio se versiona en texto, no en binarios.** Acá en el repo viven
   definiciones, scripts y notas; los `.qcow2` y `.iso` están en `.gitignore`.

## Primer montaje (≈ 12 h, repartidas)

| Paso | Qué | Horas |
| --- | --- | --- |
| 1 | Preparar el volumen (comprar si aplica), `cryptsetup luksFormat`, montaje en `/lab` | 2 |
| 2 | Mover la caché de libvirt y las imágenes a `/lab` (`/etc/libvirt/qemu.conf`) | 1 |
| 3 | Crear las redes virtuales aislada y NAT (`virsh net-define`) | 1 |
| 4 | Instalar `deb-lab` y `win-eval`, convertir cada una en plantilla | 4 |
| 5 | Practicar clonado, snapshot y rollback hasta que sea rutina | 2 |
| 6 | Script de arranque/apagado del set (`lab-up`, `lab-down`) | 2 |

## Lo que este laboratorio no es

No es un entorno de *red team* contra objetivos reales. La carrera es de
**defensa**: el laboratorio sirve para entender el ataque lo suficiente como
para defender, y para eso alcanza con tráfico propio y máquinas propias. Todo
ejercicio ofensivo se hace contra infraestructura propia o autorizada por
escrito; la materia *Ética Profesional* y *Derecho Aplicado a la Defensa
Nacional* existen por algo, y arrancan antes de que tengas las herramientas.
