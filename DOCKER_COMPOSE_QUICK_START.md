# Docker Compose 빠른 시작 가이드 (Windows)

## ⚡ 3단계로 시작하기

### 1. Docker Desktop 설치
- 다운로드: https://www.docker.com/products/docker-desktop
- 설치 후 재시작
- Docker Desktop 실행 확인

### 2. 환경 설정
```powershell
# .env 파일 생성 (프로젝트 루트)
# 아래 내용 추가
```

`.env` 파일:
```env
NODE_ENV=development
PORT=3080
MONGO_URI=mongodb://mongodb:27017/LibreChat
MEILI_HOST=http://meilisearch:7700
MEILI_MASTER_KEY=change-this-master-key
JWT_SECRET=change-this-jwt-secret
JWT_REFRESH_SECRET=change-this-refresh-secret
SESSION_EXPIRES_IN=86400000
DOMAIN_SERVER=http://localhost:3080
DOMAIN_CLIENT=http://localhost:3080
UID=1000
GID=1000
```

```powershell
# librechat.yaml 생성
Copy-Item librechat.example.yaml librechat.yaml
```

### 3. 실행
```powershell
# 모든 서비스 시작
docker-compose up -d

# 접속: http://localhost:3080

# 첫 사용자 생성
docker-compose exec api npm run create-user
```

---

## 🔧 주요 명령어

```powershell
# 시작
docker-compose up -d

# 중지
docker-compose down

# 로그 보기
docker-compose logs -f

# 상태 확인
docker ps

# 재시작
docker-compose restart api
```

---

## 🐛 문제 해결

| 문제 | 해결 |
|------|------|
| Docker 실행 안됨 | Docker Desktop 시작 |
| 포트 충돌 | `netstat -ano \| findstr :3080` |
| 컨테이너 안 뜸 | `docker-compose logs api` |

---

자세한 내용은 `DOCKER_COMPOSE_WINDOWS.md` 참조

