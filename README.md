# cuadra

## About

cuadra is an open source hardware Eurorack dual square wave oscillator module by [piruetas](https://piruetas.xyz), part of popusintes. It has two independent channels, each with a frequency potentiometer, an FM input jack and a square wave output jack, built around one ICM7556 (CMOS dual 555 timer). The board is made in KiCad 10 with its own local symbol, footprint and 3D model libraries, included in each revision's `bibliotecas/` folder. It is in development: the schematic is drawn, the board layout and the panel are not done yet.

## Acerca de

Oscilador de onda cuadrada doble eurorack de [piruetas](https://piruetas.xyz), parte de popusintes, hecho en KiCad y publicado como hardware de código abierto.

En desarrollo: el esquemático está dibujado, falta la placa y el panel.

## Características

- 2 canales independientes, cada uno con un potenciómetro de frecuencia, una entrada de FM y una salida de onda cuadrada.
- Un solo integrado: ICM7556, el 555 doble en versión CMOS. Cada canal es un oscilador astable de una sola resistencia: la salida carga y descarga el condensador de tiempo a través del potenciómetro, así que la onda sale cuadrada (ciclo de trabajo cercano al 50 %) sin usar el pin de descarga.
- Rango de frecuencia calculado (sin medir) con f ≈ 0.72 / (R · C), con C = 100 nF y R entre 1 kΩ y 101 kΩ: de unos 70 Hz a unos 7 kHz por canal.
- Entrada de FM: la entrada entra por 100 kΩ al pin de control (CONT) del 555, filtrada con 1 nF a GND. Sin cable enchufado no hace nada.
- Salida con acoplamiento AC: 1 kΩ en serie, condensador de 1 µF y 100 kΩ a GND, así que la cuadrada de 0 a 12 V queda centrada en 0 V (unos 12 Vpp).
- Potenciómetros lineales B100k de 9 mm (Alpha RD901F, de [Thonk](https://www.thonk.co.uk/)), conectados como resistencia variable.
- Jacks mono de 3.5 mm QingPu WQP-PJ398SM (tipo Thonkiconn).
- Alimentación por header Eurorack de 10 pines (2x05). Solo usa +12V, con diodo Schottky 1N5817 de protección contra polaridad inversa; -12V no se usa.

## Circuito

Por canal (canal 1 / canal 2):

| Función | Canal 1 | Canal 2 |
| --- | --- | --- |
| Mitad del ICM7556 | U1A | U1B |
| Resistencia mínima de tiempo | R3 (1k) | R4 (1k) |
| Potenciómetro de frecuencia | RV1 (100k) | RV2 (100k) |
| Condensador de tiempo | C1 (100n) | C2 (100n) |
| Entrada de FM | J1 → R1 (100k) → CONT, C3 (1n) a GND | J2 → R2 (100k) → CONT, C4 (1n) a GND |
| Salida | R5 (1k) → C8 (1u) → J4, R7 (100k) a GND | R6 (1k) → C9 (1u) → J5, R8 (100k) a GND |

Comunes: J3 (header de alimentación), D1 (1N5817), C5 y C6 (100n de desacople) y C7 (10u).

El ERC de KiCad da 0 errores y 0 advertencias.

## Revisiones

- `v0.1 rev-a`: en desarrollo desde octubre 2026. Esquemático completo; la placa está vacía.

### Pendientes para rev-a

Para el producto mínimo viable (dos osciladores que suenen en un rack):

- [ ] Cambiar la huella de RV1 y RV2. Hoy usan `POT_TH_Alps_RK09K_Single_Vertical`, la misma de atenu rev-a y suma rev-a, que tiene los pads 1 y 3 intercambiados respecto a las patas del Alpha RD901F. En cuadra eso haría que al girar en sentido horario la frecuencia baje. Usar `Potenciometro_Alpha_RD901F_Vertical` de la biblioteca `popusintes`, como en atenu rev-b.
- [ ] Revisar huellas y códigos LCSC que no siguen el resto del proyecto:
  - U1 usa `Package_SO:SOIC-14_3.9x8.7mm_P1.27mm` de la biblioteca global de KiCad y no tiene código LCSC.
  - D1 usa `Diode_THT:D_DO-41_SOD81_P10.16mm_Horizontal` de la biblioteca global, aunque la biblioteca local ya trae el 1N5817W SMD (C169541).
  - C3 y C4 (1n) no tienen código LCSC.
- [ ] Decidir R3 y R4: el commit `6141842` habla de un resistor THT para afinar, pero hoy son 0805. Si van en THT se pueden cambiar a mano para mover el rango de frecuencia.
- [ ] Decidir el nivel de salida: la salida actual es de unos 12 Vpp, un poco más que los 10 Vpp habituales en Eurorack.
- [ ] Diseñar la placa (`cuadra-v-0-rev-a.kicad_pcb` está vacío). Tiene los mismos controles que atenu (2 potenciómetros, 4 jacks), así que probablemente calza la misma distribución y el panel de 5 HP.
- [ ] Generar gerbers y archivos de taladrado (`hardware/cuadra-v-0-rev-a-fab`).

Para dejarlo igual que los otros proyectos de popusintes:

- [ ] Panel en OpenSCAD (`panel/cuadra-v-0-rev-a`) con el submódulo [popusintes-cajas-paneles](https://github.com/piruetasxyz/popusintes-cajas-paneles) en `terceros/`.
- [ ] Documentación en `docs/index.md`, publicada en <https://piruetas.xyz/cuadra/>, y `kicad-retrata.yml` para las capturas y la Bill of materials.
- [ ] Licencias: `LICENSE.md`, carpeta `LICENSES`, `REUSE.toml` y `requirements.txt`, copiados de atenu.
- [ ] Sacar del repositorio la carpeta `.easyeda_cache` (está en git, pero el patrón `*-cache*` del [.gitignore](./.gitignore) no la cubre): agregar `.easyeda_cache/` al `.gitignore` y correr `git rm -r --cached hardware/cuadra-v-0-rev-a/.easyeda_cache`.

## Estructura del repositorio

- [hardware/cuadra-v-0-rev-a](./hardware/cuadra-v-0-rev-a): proyecto de KiCad (esquemático y placa), los archivos fuente del diseño.
- [hardware/cuadra-v-0-rev-a/bibliotecas](./hardware/cuadra-v-0-rev-a/bibliotecas): símbolos, huellas y modelos 3D usados por el proyecto, generados con `partes-jlcpcb/generar_biblioteca.py`. Van en el repositorio para que el proyecto de KiCad se abra sin instalar bibliotecas adicionales.

## Créditos

- Aarón Montoya-Moraga: investigación, esquemático, PCB, panel, fabricación y documentación.

## Licencia

cuadra es (c) 2026 piruetas SpA / Aarón Montoya-Moraga.

Las licencias todavía no están agregadas a este repositorio. Siguiendo a los otros proyectos de popusintes, la idea es:

- Hardware (`hardware/`): CERN-OHL-P-2.0.
- Panel (`panel/`) y documentación (`docs/`): CC-BY-SA-4.0.
- El nombre, logo y marca de piruetas no están cubiertos por estas licencias.
