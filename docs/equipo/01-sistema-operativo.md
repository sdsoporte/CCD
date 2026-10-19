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

## Almacenamiento: verificado, hay un solo slot

Máquina identificada: **Dell Latitude 5430**, placa `01Y2TP`. El manual oficial
(*Setup and Specifications → Internal slots*) declara exactamente dos slots M.2:
uno **2230 para WiFi/Bluetooth** (ocupado por el CNVi de Intel) y **uno solo
2230/2280 para SSD** (ocupado por el SN530 de 512 GB). No hay segundo slot para
datos.

La topología PCIe del equipo lo confirma: el único root port x4 (`00:1c.0`)
alimenta el SSD, el x1 (`00:1d.0`) alimenta el lector de tarjetas, y los dos
root ports de Thunderbolt (`00:07.0/07.1`) están vacíos. No queda ningún enlace
libre.

El camino de "poner el laboratorio en un segundo NVMe interno" **no existe en
esta máquina**. Las opciones reales son tres:

| Opción | A favor | En contra | Costo |
| --- | --- | --- | --- |
| **A. Reemplazar el SSD interno por 2 TB** | Velocidad nativa, un solo dispositivo, 2 TB en un solo lugar | Migrar o reinstalar 268 GB de sistema; tocar hardware; el disco viejo queda libre solo si hay dónde ponerlo | US$ 100–160 (NVMe) |
| **B. SSD externo USB-C de 1–2 TB** | Cero riesgo sobre el sistema actual; se puede comprar y usar el mismo día | ~1 GB/s por USB 3.2 Gen2 en el mejor caso; latencia peor para I/O pesado de VMs | US$ 80–130 |
| **C. A + B (recomendado)** | 2 TB internos para sistema + laboratorio activo, y el SN530 de 512 GB pasa a una carcasa USB-C como respaldo y disco de forense | Cuesta las dos cosas y algo de trabajo de migración | US$ 115–185 |

En las tres, el cifrado entra en el mismo movimiento: migrar es la única
oportunidad barata de tener LUKS sin reinstalar dos veces.

**Dato a verificar antes de comprar:** si el SN530 instalado es formato **2230**
(lo habitual en este modelo, montado en un soporte) o **2280**. Cambia qué
dispositivo comprar y si hace falta el soporte de 2280. Se ve abriendo la tapa;
no se deduce por software.

## Decisión pendiente (tuya)

1. ¿Opción A, B o C? Y si es A/C: ¿migración por clonado o reinstalación limpia
   con LUKS desde cero? (la reinstalación limpia es la oportunidad de dejar el
   sistema cifrado, pero cuesta un fin de semana)
2. ¿Se compra en noviembre o en enero? Ni la preparación ni el arranque de
   cursada dependen de esto; solo el laboratorio completo.
