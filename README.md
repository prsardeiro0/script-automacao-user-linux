# script-automacao-user-linux
SCRIPT DE AUTOMAÇÃO DE CRIAÇÃO DE USUARIOS E PERMISSÕES 

#!/bin/bash

# Arquivo de configuração
CONFIG_FILE="configuracao.txt"

# Função para criar um grupo se não existir
criar_grupo() {
  local grupo="$1"
  if ! getent group "$grupo" > /dev/null; then
    echo "Criando grupo: $grupo"
    sudo groupadd "$grupo"
  else
    echo "Grupo já existe: $grupo"
  fi
}

# Função para criar um usuário se não existir
criar_usuario() {
  local usuario="$1"
  local grupo="$2"
  local diretorio="$3"
  if ! id -u "$usuario" > /dev/null 2>&1; then
    echo "Criando usuário: $usuario"
    sudo useradd -m -d "$diretorio" -g "$grupo" "$usuario"
  else
    echo "Usuário já existe: $usuario"
  fi
}

# Função para criar um diretório se não existir e definir permissões
criar_diretorio() {
  local diretorio="$1"
  local permissao="$2"
  if [ ! -d "$diretorio" ]; then
    echo "Criando diretório: $diretorio"
    sudo mkdir -p "$diretorio"
    sudo chmod "$permissao" "$diretorio"
    echo "Definindo permissões $permissao para $diretorio"
  else
    echo "Diretório já existe: $diretorio"
  fi
}

# Ler o arquivo de configuração linha por linha
while IFS=':' read -r usuario grupo diretorio permissao; do
  echo "Processando usuário: $usuario"
  criar_grupo "$grupo"
  criar_usuario "$usuario" "$grupo" "$diretorio"
  criar_diretorio "$diretorio" "$permissao"
  echo "-------------------------"
done < "$CONFIG_FILE"

echo "Infraestrutura criada com sucesso!"
