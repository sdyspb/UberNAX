# Распределённые вычисления на базе UberNAX

## Стек "Фреймворк – Кэш – Сервер – Хранилище"

### 1. Роль UberNAX
- **AI-нода** – получает видеопотоки от камер, выполняет инференс через NPU, отправляет метаданные (объекты, треки) в Redis.
- **Кэш-сервер** – может быть развёрнут на втором UberNAX или в контейнере на той же машине (Redis).
- **Бэкенд** – FastAPI / Node.js, подписывается на события из Redis, сохраняет результаты в NAS.
- **NAS** – отдельный экземпляр UberNAX в режиме хранилища (или общая сетевая папка).

### 2. Пример конфигурации (docker-compose.yml для AI-сервера)
```yaml
version: '3'
services:
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
  backend:
    build: ./api
    depends_on:
      - redis
    environment:
      - REDIS_URL=redis://redis:6379
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
