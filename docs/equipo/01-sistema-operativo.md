# Decisión: sistema operativo y distro

## Pregunta

¿Conviene cambiar de distro para la carrera, o adaptar el entorno sobre lo que
ya hay?

## Recomendación

**No cambiar la distro.** Mantener Ubuntu 26.04 LTS como sistema base y
**agregar capas**: laboratorio en VMs, volumen cifrado, redes aisladas.

## Por qué

1. **La ventana de soporte cubre la carrera completa.** Ubuntu 26.04 es LTS
   (abril 2026): soporte estándar de 5 años → **abril 2031**, justo después de
   que termines el cuatrimestre final. Cambiar a una distro de ciclo corto
   (Fedora, 13 meses) te obliga a hacer *upgrades* de sistema en medio de
   parciales.
2. **El rendimiento no lo decide la distro.** El techo medido de esta máquina es
   disco (196 GB libres) y ausencia de GPU. Ninguna distro agrega disco ni GPU.
   Una reinstalación no mueve ninguno de los dos números.
3. **Ya tenés un entorno de trabajo funcionando**: Pi, Node 24, Bun, Rust, uv,
   Docker, Engram, Coach/trajectory, KVM. Reinstalar cuesta días de re-setup y
   el beneficio académico es cero.
4. **Lo que la carrera necesita de Linux es el kernel, no el escritorio.**
   Sistemas Operativos I y II, Forense y Criptografía se estudian con
   `strace`, `/proc`, `cryptsetup` y `tcpdump`, no con un tema distinto de GNOME.

Cambiar de distro es una decisión de gusto, y es legítima — pero no es una
decisión de rendimiento, y no la justifica la carrera.

## Qué sí hacer: capas sobre la base actual

| Capa | Qué | Estado |
| --- | --- | --- |
| Laboratorio | VMs KVM/libvirt sobre almacenamiento dedicado | Guía en `02-laboratorio.md` |
| Cifrado | Volumen LUKS para el laboratorio (no hace falta tocar el sistema) | Recomendado antes de cargar datos de práctica |
| Redes aisladas | Redes virtuales libvirt + la NIC cableada para segmentos reales | Guía en `02-laboratorio.md` |
| Higiene | Recuperar ~50 GB de Docker, snaps y journal | Ver `00-diagnostico.md` |

## Alternativas, si igual querés cambiar

Se documentan para que la decisión sea informada, no para empujarla.

| Opción | A favor | En contra |
| --- | --- | --- |
| **Quedarse en Ubuntu 26.04 LTS** (recomendado) | Soporte hasta 2031, entorno intacto, hardware soportado | Nada relevante |
| Debian 13 stable | Menos *churn*, muy cercano a lo que enseña SO I/II, soporte largo | Re-setup completo del entorno de trabajo por ganancia nula |
| Fedora Workstation | Kernel nuevo, SELinux activo (útil para estudiar controles) | Ciclo de 13 meses: *upgrades* obligatorios en plena cursada |
| Kali/Parrot como sistema principal | Trae el toolkit ofensivo | Mala práctica para un equipo de trabajo diario: mezcla herramientas de laboratorio con datos reales. Kali va **como VM**, no como host |
| Doble boot Windows | Cobertura de Windows Server / AD | Reiniciar para cambiar de materia. Windows va como VM |

## Dónde sí vale la pena una instalación nueva

Un caso concreto: si el disco tiene un **segundo slot M.2 libre**, instalar el
laboratorio en un NVMe interno dedicado y dejar el sistema actual intacto es la
opción más limpia (rendimiento de VM mucho mayor que USB-C, y aislamiento físico
entre trabajo y práctica). Es la única reinstalación que se justifica, y solo si
el slot existe.

## Decisión pendiente (tuya)

1. ¿Se compra el NVMe (externo o interno según slot) en noviembre, o se arranca
   con los ~50 GB recuperados y se compra en enero?
2. ¿El cifrado va sobre un volumen dedicado del laboratorio, o se evalúa cifrar
   también el sistema en algún receso?

Ninguna de las dos bloquea la preparación. La primera bloquea el laboratorio
completo; la segunda, no bloquea nada hasta que haya datos de práctica.
