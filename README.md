# duplicatedpoliciesOCI
Finder script to search duplicated policies on OCI
Acces cloud shell de OCI
Cloud Shell
Luego ejecuta: oci iam compartment list --all --compartment-id-in-subtree true \
  | jq -r '.data[] | select(."compartment-id" == null) | .id'
Ahora ejecuta: export TENANCY_OCID=$(oci iam compartment list --all --compartment-id-in-subtree true \
  | jq -r '.data[] | select(."compartment-id" == null) | .id')
Valida: echo $TENANCY_OCID
Ahora obtenemos todas las politicas: oci iam policy list --compartment-id $OCI_TENANCY --all > policies.json
Ahora ejecuta este script tal cual copia y pega en la consola:

cat > encontrar_duplicados_policies.sh << 'EOF'
#!/usr/bin/env bash
set -euo pipefail

INPUT="${1:-policies.json}"

# 1) Sacar todos los statements a un archivo de texto
# Soporta tanto json con raíz "data" como array directo
jq -r '
  ( .data // . )[]
  | .statements[]
' "$INPUT" > statements.txt

# 2) Buscar duplicados y sus líneas
awk '
{
  line = NR
  stmt = $0
  if (lines[stmt] == "")
    lines[stmt] = line
  else
    lines[stmt] = lines[stmt] "," line
  count[stmt]++
}
END {
  for (s in count) {
    if (count[s] > 1) {
      printf "STATEMENT DUPLICADO (%d veces):\n%s\nLineas: %s\n\n", count[s], s, lines[s]
    }
  }
}' statements.txt
EOF
