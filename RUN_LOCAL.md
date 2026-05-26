# Chạy local - FIT4110 Lab 04

Tài liệu này hướng dẫn cách chạy IoT ingestion service bằng Docker và kiểm tra lại bằng Newman/Postman.

## Yêu cầu trước khi chạy

- Đã cài Docker Desktop và Docker đang chạy.
- Đã cài Node.js và npm.
- Các lệnh bên dưới dùng cho PowerShell trên Windows.

## 1. Cài dependencies cho Newman/Prism/Spectral

```powershell
npm.cmd install
```

Nếu PowerShell không chặn `npm`, có thể dùng:

```powershell
npm install
```

## 2. Build Docker image

```powershell
docker build -t fit4110/iot-ingestion:lab04 .
```

## 3. Chạy container

Chạy foreground để xem log trực tiếp:

```powershell
docker run --rm --name fit4110-iot-lab04 -p 8000:8000 --env-file .env.example fit4110/iot-ingestion:lab04
```

Chạy detached ở background:

```powershell
docker run -d --rm --name fit4110-iot-lab04 -p 8000:8000 --env-file .env.example fit4110/iot-ingestion:lab04
```

## 4. Kiểm tra health endpoint

```powershell
Invoke-WebRequest -UseBasicParsing http://localhost:8000/health | Select-Object -ExpandProperty Content
```

Kết quả mong đợi:

```json
{"status":"ok","service":"iot-ingestion","version":"0.4.0"}
```

Lệnh tương đương bằng `curl`:

```powershell
curl http://localhost:8000/health
```

## 5. Chạy Newman test trên container

```powershell
npm.cmd run test:local
```

Kết quả mong đợi:

```text
requests:   11 executed, 0 failed
assertions: 19 executed, 0 failed
```

Report được sinh tại:

```text
reports/newman-lab04-local.xml
reports/newman-lab04-local.html
```

## 6. Artefact cần nộp

Các artefact cho team `team-iot`:

```text
Dockerfile
.dockerignore
.env.example
RUN_LOCAL.md
contracts/team-iot.openapi.yaml
postman/collections/team-iot.postman_collection.json
postman/environments/team-iot_local.postman_environment.json
reports/newman-lab04-local.xml
reports/newman-lab04-local.html
```

## 7. Bằng chứng image tag và registry

Image local của lab:

```text
fit4110/iot-ingestion:lab04
```

Image đã push lên GHCR:

```text
ghcr.io/hung981vpp/team-iot:v0.1.0-team-iot
```

Digest sau khi push thành công:

```text
sha256:e7b715cb61a8fec07af5cb0f548f14b68c2a1c94509989a6f507b0e24f5aeeb3
```

Nếu cần push lại sau khi login:

```powershell
docker login ghcr.io
docker push ghcr.io/hung981vpp/team-iot:v0.1.0-team-iot
```

## 8. Dừng container

```powershell
docker stop fit4110-iot-lab04
```

## 9. Lệnh nhanh bằng Makefile

Nếu máy có `make`, có thể dùng:

```powershell
make install
make lint
make build
make run
make test-docker
make stop
```

Các lệnh PowerShell tương đương:

```powershell
npm.cmd install
npm.cmd run lint:openapi
docker build -t fit4110/iot-ingestion:lab04 .
docker run --rm --name fit4110-iot-lab04 -p 8000:8000 --env-file .env.example fit4110/iot-ingestion:lab04
npm.cmd run test:local
docker stop fit4110-iot-lab04
```
