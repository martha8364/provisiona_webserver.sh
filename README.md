# provisiona_webserver.sh
Neste projeto iremos criar um script onde será provisionado um servidor web automaticamente. Um servidor web é um software e hardware que usa HTTP (Hypertext Transfer Protocol) e outros protocolos para responder a solicitações de clientes feitas pela World Wide Web.
#!/bin/bash

# Verifica se o script está sendo executado como root
if [ "$(id -u)" -ne 0 ]; then
  echo "Este script deve ser executado como root. Use sudo."
  exit 1
fi

echo ">>> Atualizando repositórios e pacotes..."
apt update -y && apt upgrade -y

echo ">>> Instalando Apache..."
apt install apache2 -y

echo ">>> Criando página HTML padrão..."
cat <<EOF > /var/www/html/index.html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Servidor Apache Provisionado</title>
</head>
<body>
    <h1>Servidor Web Apache instalado com sucesso!</h1>
    <p>Provisionado automaticamente via script Bash.</p>
</body>
</html>
EOF

echo ">>> Ajustando permissões do diretório web..."
chown -R www-data:www-data /var/www/html
chmod -R 755 /var/www/html

echo ">>> Ativando e iniciando o Apache..."
systemctl enable apache2
systemctl start apache2

echo ">>> Liberando porta 80 no firewall (se aplicável)..."
ufw allow 'Apache' 2>/dev/null || echo "Firewall UFW não está ativo ou instalado."

echo ">>> Provisionamento concluído. Acesse: http://<IP_DA_MAQUINA>"
