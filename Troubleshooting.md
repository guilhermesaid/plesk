# 🧠 Troubleshooting Completo no Plesk (Red Hat-based)

Guia definitivo para identificar e resolver problemas em servidores com **Plesk** rodando em **distribuições Red Hat-based** como **Rocky Linux**, **AlmaLinux**, **CentOS** ou **RHEL**.

---

## 📌 1. Informações Básicas do Sistema
```bash
hostnamectl
uptime
top -o %CPU   # ou htop
free -m
df -h
```

---

## 🧩 2. Verificar status dos serviços principais do Plesk
```bash
plesk sbin status | grep -v run
plesk repair all -n
systemctl list-units --type=service | grep -Ei 'mysql|httpd|nginx|php|plesk|dovecot|postfix'
```

---

## 🔧 3. Ver Logs Críticos do Sistema e do Plesk

| Componente        | Log Path                                      |
|-------------------|-----------------------------------------------|
| Sistema           | `/var/log/messages`                           |
| Plesk Painel      | `/var/log/plesk/panel.log`                    |
| Plesk Installer   | `/var/log/plesk/install/autoinstaller3.log`   |
| Apache            | `/var/log/httpd/error_log` ou `/var/log/apache2/error.log` |
| NGINX             | `/var/log/nginx/error.log`                    |
| MySQL             | `/var/log/mysqld.log` ou `/var/log/mysql/error.log` |
| Mail (Postfix)    | `/var/log/maillog`                            |
| FTP (ProFTPd)     | `/var/log/secure` ou `/var/log/messages`      |
| Fail2Ban          | `/var/log/fail2ban.log`                       |

---

## 💥 4. Logs do Site Específico
```bash
/var/www/vhosts/DOMINIO.COM/logs/error_log
/var/www/vhosts/DOMINIO.COM/logs/access_log
```

---

## 🐘 5. Logs de PHP e Estouro de Variável

### Ver erro de variável topada ou memory_limit excedido:
```bash
grep -i 'Allowed memory size' /var/www/vhosts/*/logs/error_log
```

### Logs de FPM (se estiver ativo):
```bash
ls /var/log/plesk-php*
cat /var/log/plesk-phpXX-fpm/error.log
```

---

## 🔍 6. Ver erros de permissão / ownership quebrado
```bash
find /var/www/vhosts/DOMINIO.COM/httpdocs -not -user nome_da_conta
plesk repair fs -y
```

---

## 🧱 7. Troubleshooting NGINX e Apache (proxy reverso)
```bash
plesk bin domain --info DOMINIO.COM | grep "nginx\|apache"
plesk repair web -y
```

---

## 🛠️ 8. Testes rápidos de aplicação
```bash
curl -v http://dominio.com
curl -v --resolve dominio.com:80:127.0.0.1 http://dominio.com
```

---

## 🔐 9. Logs de segurança (SSH, sudo, brute force, fail2ban)
```bash
cat /var/log/secure | grep -Ei 'fail|denied|sudo|ssh'
cat /var/log/fail2ban.log
```

---

## 🔄 10. Reiniciar serviços com segurança
```bash
systemctl restart nginx
systemctl restart httpd
systemctl restart mysql
systemctl restart plesk-php80-fpm   # ajuste a versão
```

---

## 🧪 11. Testes de Performance
```bash
top
iotop
vmstat 1
iostat -xz 1
```

---

## 📦 12. Testar PHP CLI direto no terminal (debug local)
```bash
/usr/bin/php -d memory_limit=128M /caminho/para/o/script.php
```

---

## 🧼 13. Corrigir permissões automaticamente
```bash
plesk repair fs -y
plesk repair mail -y
plesk repair db -y
```

---

## 📋 14. Visualizar tudo do domínio com comando Plesk
```bash
plesk bin domain --info dominio.com
```

---

## 👀 15. Ver utilização de recursos por domínio (Plesk Premium Monitoring)
```bash
plesk ext monitoring --get-stats
```

---

## 🔒 16. Ver certificados, SSL e Let's Encrypt
```bash
plesk bin certificate --info -domain dominio.com
plesk bin extension --exec letsencrypt cli.php -l
```

---

> **Autor:** Guilherme Said  
> **Objetivo:** Ser o canivete suíço do troubleshooting no Plesk para qualquer distro Red Hat-like.

