# OCI IAM Policies – Duplicate Statements Finder

📌 **ES / EN**  
Script en **Bash** para identificar **statements duplicados** dentro de las **IAM Policies** de Oracle Cloud Infrastructure (OCI).  
El script analiza un archivo JSON exportado desde OCI CLI y muestra qué statements se repiten, cuántas veces y en qué líneas aparecen.

---

## 🇪🇸 Español

### ¿Qué hace este script?

- Extrae todos los **statements** de las IAM Policies
- Detecta **duplicados exactos**
- Muestra:
  - El statement repetido
  - Número de veces que aparece
  - Líneas donde se encontró

Es especialmente útil para:
- Auditorías de seguridad
- Limpieza de políticas heredadas
- Evitar redundancia y errores en IAM

---

## 🇬🇧 English

### What does this script do?

- Extracts all **policy statements** from OCI IAM Policies
- Detects **exact duplicate statements**
- Displays:
  - The duplicated statement
  - Number of occurrences
  - Line numbers where it appears

Useful for:
- Security audits
- Cleaning up legacy IAM policies
- Reducing redundancy and IAM misconfiguration risks

---

## Requisitos / Requirements

- Acceso a **OCI Cloud Shell**
- OCI CLI
- `jq`
- `awk`

> Cloud Shell already includes OCI CLI, `jq` and `awk`.

---

## Flujo general / General flow

1. Obtener el OCID del tenancy  
2. Exportar las IAM Policies a JSON  
3. Ejecutar el script para detectar duplicados  

---

## Paso a paso (OCI Cloud Shell) / Step by step

### 1) Abrir Cloud Shell / Open Cloud Shell

Desde la consola de OCI, abre **Cloud Shell**.

---

### 2) Obtener el OCID del Tenancy / Get Tenancy OCID

```bash
oci iam compartment list --all --compartment-id-in-subtree true \
  | jq -r '.data[] | select(."compartment-id" == null) | .id'


### 3) Obtener todas las politicas // Get all policies
```bash
oci iam policy list --compartment-id $OCI_TENANCY --all > policies.json
