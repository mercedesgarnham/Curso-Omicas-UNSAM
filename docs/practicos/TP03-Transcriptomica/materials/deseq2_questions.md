# Preguntas de discusión — DESeq2 (Parte 5)

Este documento contiene preguntas por segmento pensadas para fomentar la discusión y la comprensión de los pasos del análisis con DESeq2.

## 1) Preparación y entradas
- ¿Por qué NO debemos usar TPM/FPKM/RPKM como entrada para DESeq2?
- Si tenemos resultados de Salmon, ¿qué hace `tximport` cuando usamos `countsFromAbundance = "lengthScaledTPM"`? ¿Qué problema resuelve?
- ¿Qué columnas mínimas debe contener la `sample_table` y por qué es importante la coherencia de nombres entre archivos y metadata?

## 2) Lectura de conteos (STAR `ReadsPerGene.out.tab`)
- ¿Cómo identificás si tus datos son "stranded" o "unstranded" a partir de `ReadsPerGene.out.tab` y por qué importa?
- Si notas que las columnas 3 y 4 (read1/read2) son muy diferentes entre sí, ¿qué podría estar pasando en el protocolo o en el procesamiento?

## 3) Diseño y filtrado
- ¿Qué significa incluir `~ batch + condition` en el diseño y en qué casos es indispensable?
- ¿Qué riesgos corres al filtrar genes demasiado estrictamente antes del análisis?
- En un experimento con 2 réplicas por condición, ¿cómo ajustarías el filtrado para no eliminar todos los genes?

## 4) Ejecución de DESeq2 y shrinkage
- ¿Por qué aplicamos shrinkage al `log2FoldChange` antes de reportarlo en tablas finales?
- ¿Qué diferencia práctica hay entre usar `apeglm` y `ashr` para shrinkage?
- ¿Cómo comprobarías que el contraste que pediste en `results()` corresponde al coeficiente que estás interpretando?

## 5) Visualización y QC
- En un PCA, si un par de muestras se alejan significativamente del grupo, ¿qué pasos tomarías antes de excluirlas del análisis?
- ¿Qué indicadores en un heatmap te hacen sospechar que hay un batch effect?

## 6) Análisis funcional
- ¿Por qué es importante elegir correctamente el "background" (universo) para un test de sobre-representación?
- ¿Qué problemas puede causar convertir IDs incorrectamente (pérdida de genes, asignaciones erróneas)?

## 7) Interpretación y reproducibilidad
- ¿Qué información mínima debe acompañar a un `DESeq2_all_results.csv` para que otro investigador pueda reproducir el análisis?
- ¿Por qué es útil incluir `sessionInfo()` y la `sample_table` en el repositorio?

---

Si querés, puedo añadir estas preguntas directamente en `index.md` como un bloque de preguntas al final de cada subsección, o dejar el archivo `materials/deseq2_questions.md` enlazado desde `index.md`. Indica cómo preferís integrarlo.

---

# Respuestas esperadas (breves)

Aquí se incluyen respuestas concisas a cada pregunta anterior — pensadas como guía del instructor o como feedback para los estudiantes.

## 1) Preparación y entradas — respuestas
- No usar TPM/FPKM/RPKM: DESeq2 modela conteos enteros y estima la varianza basada en la naturaleza discreta de los conteos; las medidas normalizadas por longitud/composición (TPM/FPKM) invalidan las suposiciones del modelo y producen sesgos en la inferencia.
- `tximport` con `countsFromAbundance = "lengthScaledTPM"`: convierte abundancias transcriptómicas en estimaciones de conteos corregidas por longitud y biblioteca; esto mejora la comparabilidad entre genes y permite usar estimaciones pseudo-counts en DESeq2.
- `sample_table` mínimo: `sample` (ID), `condition` (grupo), opcional `batch`, `replicate`, `file` (ruta). Coherencia de nombres es crítica para alinear columnas de la matriz de conteos con las filas de metadata.

## 2) Lectura de conteos (STAR) — respuestas
- Identificar strandedness: STAR produce 3 columnas de conteo; comparar totales por columna (col2 unstranded, col3/4 stranded) o usar herramientas como `infer_experiment.py` (RSeQC) para confirmar; la elección afecta qué columna usar.
- Columnas 3 vs 4 muy diferentes: puede indicar que la librería es stranded y que una de las lecturas es la que mapea preferentemente a genes (protocolo strand-specific), o error en el orden de archivos (R1/R2 intercambiados) o problemas en el pipeline de trimming/align.

## 3) Diseño y filtrado — respuestas
- `~ batch + condition`: controla efectos de lote (batch) que de otro modo confundirían la estimación del efecto de interés; indispensable si hay batches con distribución desigual de condiciones.
- Riesgos de filtrado estricto: perder genes reales de baja expresión, reducir la potencia estadística o introducir sesgos en el universo para análisis funcional.
- En 2 réplicas: usar un filtrado más laxo (ej. `rowSums(counts >= 5) >= 2`) o analizar con cautela y mencionar la baja potencia; preferir no aplicar umbrales muy altos.

## 4) Ejecución de DESeq2 y shrinkage — respuestas
- Por qué shrinkage: reduce estimaciones extremas de log2FC en genes con poca cobertura, mejorando reproducibilidad y evitando listas con fold-changes inflados por ruido.
- `apeglm` vs `ashr`: `apeglm` suele ofrecer shrinkage adaptativo con buenas propiedades para inferencia de LFC; `ashr` es otra alternativa; `apeglm` es la recomendada actualmente para DESeq2.
- Comprobar contraste: usar `resultsNames(dds)` para ver los nombres de coeficientes, o pasar explícitamente `contrast=c(...)` a `results()` para asegurarse del contraste calculado.

## 5) Visualización y QC — respuestas
- Si muestras se alejan en PCA: revisar metadata (batch, calidad de muestra, número de lecturas), inspeccionar FastQC/MultiQC, ver counts totales, considerar excluir solo tras justificar y documentar; probar análisis con/ sin la muestra para ver impacto.
- Indicadores de batch en heatmap: clusters que siguen batches más que condiciones, separación por batch en PCA o correlaciones altas dentro de batch.

## 6) Análisis funcional — respuestas
- Importancia del background: elegir el universo correcto evita sesgos; para RNA-seq es habitual usar los genes que pasaron el filtrado como universo en lugar de todo el genoma.
- Problemas al convertir IDs: pérdida de genes (IDs no mapeados), mapeos erróneos o duplicados; verificar cuántos genes se pierden tras conversión y reportarlo.

## 7) Interpretación y reproducibilidad — respuestas
- Información mínima para reproducir: tabla de conteos (raw), `sample_table` (metadata), script R/NB completo, versión de paquetes (`sessionInfo()`), y criterios de filtrado y umbrales usados.
- `sessionInfo()` y `sample_table`: `sessionInfo()` registra versiones de R y paquetes; `sample_table` documenta el diseño experimental y permite reproducir la asociación conteo→muestra.

---

Si querés, puedo ahora:
- Insertar las respuestas también dentro de `index.md` como comentarios ocultos o como un anexo visible (si querés que estén directamente en la clase). 
- Generar un `README.md` en `materials/` con instrucciones para el docente sobre cómo usar estas preguntas en clase.

Indicame si querés alguna de las dos opciones y lo implemento.