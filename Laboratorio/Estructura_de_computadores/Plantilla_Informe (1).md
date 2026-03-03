# Informe de Laboratorio: Estructura de Computadores

**Nombre del Estudiante:** Aguilar Ardila Gabriel Esteban 
**Fecha:** 22/02/2026 
**Asignatura:** Estructura de Computadores
 
**Enlace del repositorio en GitHub:** [agregar enlace Aquí]  
 

---

## 1. Análisis del Código Base

### 1.1. Evidencia de Ejecución
Adjunte aquí las capturas de pantalla de la ejecución del `programa_base.asm` utilizando las siguientes herramientas de MARS:
*   **MIPS X-Ray** (Ventana con el Datapath animado).
*   **Instruction Counter** (Contador de instrucciones totales).
*   **Instruction Statistics** (Desglose por tipo de instrucción).

![MIPS X-Ray](C:\Users\pc1\Desktop\Estructura_de_computadores\MIPS_XRay.png)
![No. Instrucciones](C:\Users\pc1\Desktop\Estructura_de_computadores\Contador_Numeros_Instrucciones.png)
![Tipo de Instruccion](C:\Users\pc1\Desktop\Estructura_de_computadores\Instruccion_Estadisticas.png)

### 1.2. Identificación de Riesgos (Hazards)
Completa la siguiente tabla identificando las instrucciones que causan paradas en el pipeline:

| Instrucción Causante | Instrucción Afectada | Tipo de Riesgo (Load-Use, etc.) | Ciclos de Parada |
|----------------------|----------------------|---------------------------------|------------------|
| `lw $t6, 0($t5)`     | `mul $t7, $t6, $t0`  | Load-Use y RAW                       |  4 (2 de Load-Use y 2 del mul-addu)               |
|                      |                      |                                 |                  |

### 1.2. Estadísticas y Análisis Teórico
Dado que MARS es un simulador funcional, el número de instrucciones ejecutadas será igual en ambas versiones. Sin embargo, en un procesador real, el tiempo de ejecución (ciclos) varía. Completa la siguiente tabla de análisis teórico:

| Métrica | Código Base | Código Optimizado |
|---------|-------------|-------------------|
| Instrucciones Totales (según MARS) |     95        |          94         |
| Stalls (Paradas) por iteración |      4 (2 de Load-Use y 2 del mul-addu)      |          0         |
| Total de Stalls (8 iteraciones) |       32 Stalls Totales      |          0         |
| **Ciclos Totales Estimados** (Inst + Stalls) |      95+32=127       |         94          |
| **CPI Estimado** (Ciclos / Inst) |      127/95≈1.34       |         1          |

---

## 2. Optimización Propuesta

### 2.1. Evidencia de Ejecución (Código Optimizado)
Adjunte aquí las capturas de pantalla de la ejecución del `programa_optimizado.asm` utilizando las mismas herramientas que en el punto 1.1:
*   **MIPS X-Ray**.
*   **Instruction Counter**.
*   **Instruction Statistics**.

![MIPS X-Ray OP](C:\Users\pc1\Desktop\Estructura_de_computadores\MIPS_XRays_OP.png)
![No. Instrucciones OP](C:\Users\pc1\Desktop\Estructura_de_computadores\No_Instrucciones_OP.png)
![Tipo de Instruccion OP](C:\Users\pc1\Desktop\Estructura_de_computadores\Instruccion_Estadisticas.png)

### 2.2. Código Optimizado
Pega aquí el fragmento de tu bucle `loop` reordenado:

```asm
loop:
    # --- Condición de salida ---
    beq $t3, $t2, fin     # Si i == tamano, salir del bucle
    
    # --- Cálculo de dirección de memoria ---
    sll $t4, $t3, 2       # Desplazamiento: t4 = i * 4
    addu $t5, $s0, $t4    # t5 = dirección de X[i]
    
    # --- Carga de dato ---
    lw $t6, 0($t5)        # Leer X[i]
    
    # Optimizacion No. 1: se mueve addu $t9 aquí para eliminar el stall Load-Use (lw - mul) 
    addu $t9, $s1, $t4    # t9 = dirección de Y[i] 
    
    # --- Operación aritmética ---
    mul $t7, $t6, $t0     # t7 = X[i] * A  (sin stall: $t6 queda listo de una vez)
    
    # Optimizacion 2: se mueve addi aquí para eliminar el stall mul-addu (mul - addu)
    addi $t3, $t3, 1      # i = i + 1 
    
    addu $t8, $t7, $t1    # t8 = t7 + B (sin stall: $t7 mismo caso al anterior queda listo de una)
    
    # --- Almacenamiento de resultado ---
    sw $t8, 0($t9)        # Guardar resultado en Y[i]
    
    j loop

fin:
```

### 2.2. Justificación Técnica de la Mejora
Explica qué instrucción moviste y por qué colocarla entre el `lw` y el `mul` elimina el riesgo de datos:
> [Tu explicación aquí]

---

## 3. Comparativa de Resultados

| Métrica | Código Base | Código Optimizado | Mejora (%) |
|---------|-------------|-------------------|------------|
| Ciclos Totales | 127 | 94 | 26% |
| Stalls (Paradas) | 32 | 0 | 100% |
| CPI | 1.34 | 1.00 | 25.4% |

---

## 4. Conclusiones
¿Qué impacto tiene la segmentación en el diseño de software de bajo nivel? ¿Es siempre posible eliminar todas las paradas?
> [Tus conclusiones aquí]
