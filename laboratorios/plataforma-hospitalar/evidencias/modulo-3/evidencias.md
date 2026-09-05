##  Módulo 3 - Evidências 
### 1. Versões
**Comando executado:** `docker version; docker compose version; python --version`

```text
Client:
 Version:           28.0.4
 API version:       1.48
 Go version:        go1.23.7
 Git commit:        b8034c0
 Built:             Tue Mar 25 15:07:48 2025
 OS/Arch:           windows/amd64
 Context:           desktop-linux

Server: Docker Desktop 4.40.0 (187762)
 Engine:
  Version:          28.0.4
  API version:      1.48 (minimum version 1.24)
  Go version:       go1.23.7
  Git commit:       6430e49
  Built:            Tue Mar 25 15:07:22 2025
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.7.26
  GitCommit:        753481ec61c7c8955a23d6ff7bc8e4daed455734
 runc:
  Version:          1.2.5
  GitCommit:        v1.2.5-0-g59923ef
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
Docker Compose version v2.34.0-desktop.1
Python 3.13.2
```

---

### 2. Execusão dos serviços
**Comando executado:** `docker compose -f infra/compose.servicos.yml ps`

```text
NAME                       IMAGE                    COMMAND                  SERVICE            CREATED          STATUS                    PORTS
infra-db_elegibilidade-1   infra-db_elegibilidade   "docker-entrypoint.s…"   db_elegibilidade   29 seconds ago   Up 25 seconds (healthy)   
infra-db_exames-1          infra-db_exames          "docker-entrypoint.s…"   db_exames          29 seconds ago   Up 25 seconds (healthy)   
infra-elegibilidade-1      infra-elegibilidade      "python -m uvicorn h…"   elegibilidade      28 seconds ago   Up 20 seconds (healthy)   0.0.0.0:18001->8000/tcp, [::]:18001->8000/tcp
infra-exames-1             infra-exames             "python -m uvicorn h…"   exames             28 seconds ago   Up 17 seconds (healthy)   0.0.0.0:18002->8000/tcp, [::]:18002->8000/tcp
```

---
**Comando executado:** `curl.exe -i "http://localhost:$env:ELEGIBILIDADE_PORT/health"`

```text
HTTP/1.1 200 OK
date: Fri, 04 Sep 2026 22:49:49 GMT
server: uvicorn
content-length: 41
content-type: application/json

{"status":"ok","servico":"elegibilidade"}
```


---

**Comando executado:** `curl.exe -i "http://localhost:$env:EXAMES_PORT/health"`

```text
HTTP/1.1 200 OK
date: Sat, 05 Sep 2026 18:27:56 GMT
server: uvicorn
content-length: 34
content-type: application/json

{"status":"ok","servico":"exames"}
```


---

**Comando executado:** `curl.exe -i -X POST "http://localhost:$env:EXAMES_PORT/exames" `  -H "Content-Type: application/json" `  -d '{\"beneficiario_id\":\"paciente-001\",\"codigo_exame\":\"HEM-001\"}'`

```text
HTTP/1.1 201 Created
date: Sat, 05 Sep 2026 18:31:36 GMT
server: uvicorn
content-length: 102
content-type: application/json

{"beneficiario_id":"paciente-001","codigo_exame":"HEM-001","solicitacao_id":2,"situacao":"solicitado"}
```

---

### 3. Falha Parcial
**Comando executado:** `docker compose -f infra/compose.servicos.yml stop elegibilidade`
```text
 ✔ Container infra-elegibilidade-1  Stopped
```

 ---

**Comando executado:** `docker compose -f infra/compose.servicos.yml ps`
```text
 NAME                       IMAGE                    COMMAND                  SERVICE            CREATED          STATUS                    PORTS
infra-db_elegibilidade-1   infra-db_elegibilidade   "docker-entrypoint.s…"   db_elegibilidade   22 minutes ago   Up 22 minutes (healthy)   
infra-db_exames-1          infra-db_exames          "docker-entrypoint.s…"   db_exames          22 minutes ago   Up 22 minutes (healthy)   
infra-exames-1             infra-exames             "python -m uvicorn h…"   exames             22 minutes ago   Up 22 minutes (healthy)   0.0.0.0:18002->8000/tcp, [::]:18002->8000/tcp
```

---

**Comando executado:** `curl.exe -i -X POST "http://localhost:$env:EXAMES_PORT/exames" `  -H "Content-Type: application/json" `  -d '{\"beneficiario_id\":\"paciente-001\",\"codigo_exame\":\"HEM-001\"}'`

```text
HTTP/1.1 503 Service Unavailable
date: Sat, 05 Sep 2026 18:51:51 GMT
server: uvicorn
content-length: 48
content-type: application/json

{"detail":{"codigo":"dependencia_indisponivel"}}
```

---

### 4. Verificar as fronteiras sem depender do Compose
**Comando executado:** `python -m pytest tests/test_service_boundaries.py -q `
```text
....                                                                                                                                                                                                                                                                                                [100%]
4 passed in 1.10s
```



