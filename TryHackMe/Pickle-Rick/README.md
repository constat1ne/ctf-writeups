# Pickle Rick — TryHackMe

## 📌 Informações

| Campo | Detalhe |
|---|---|
| Plataforma | TryHackMe |
| Dificuldade | Fácil |
| OS | Linux |
| Tags | Web Enumeration, Command Injection, Privilege Escalation |

---

## 🔍 1. Reconhecimento

Inicio o scan com Nmap:

```bash
nmap -sC -sV -oN nmap.txt <IP>
```

**Resultado:**
- Porta 80 — HTTP (Apache)
- Porta 22 — SSH

---

## 🌐 2. Enumeração Web

Acesso o site no navegador — tema do Rick and Morty, objetivo é encontrar 3 ingredientes.

Verifico o código fonte da página e encontro um comentário com o usuário:
Username: R1ckRul3s
Enumero diretórios com Gobuster:

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt
```

**Descobertas:**
- `/robots.txt` — contém uma string estranha: `Wubbalubbadubdub`
- `/login.php` — painel de login

---

## 🔑 3. Acesso ao Painel

Utilizo as credenciais encontradas:

- **Usuário:** `R1ckRul3s`
- **Senha:** `Wubbalubbadubdub`

Acesso o painel com sucesso ✅

---

## 💉 4. Command Injection

O painel possui um campo de execução de comandos. Testo:

```bash
whoami
```

Retorna `www-data` — execução confirmada ✅

Listo os arquivos do diretório:

```bash
ls -la
```

Encontro o arquivo `Sup3rS3cretPickl3Ingred.txt` — **primeiro ingrediente** ✅

Como `cat` está bloqueado, uso alternativa:

```bash
less Sup3rS3cretPickl3Ingred.txt
```

---

## 🔍 5. Segundo Ingrediente

Navego até o home do usuário rick:

```bash
ls /home/rick
```

Encontro `second ingredients`:

```bash
less /home/rick/"second ingredients"
```

**Segundo ingrediente capturado** ✅

---

## ⬆️ 6. Escalação de Privilégios

Verifico permissões sudo:

```bash
sudo -l
```

Resultado: posso rodar tudo como root sem senha.

```bash
sudo ls /root
sudo less /root/3rd.txt
```

**Terceiro ingrediente capturado** ✅

---

## 📝 Resumo

| Ingrediente | Onde encontrar | Técnica |
|---|---|---|
| 1º | Diretório web | Command Injection |
| 2º | /home/rick | Command Injection |
| 3º | /root | Sudo Misconfiguration |

---

## 🛠️ Ferramentas Utilizadas

- Nmap
- Gobuster
- Burp Suite
- Netcat
