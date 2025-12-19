# LibreChat macOS 빠른 시작 가이드

## ⚡ 빠른 설치 (5분)

### 1. 필수 소프트웨어 설치
```bash
# Homebrew 설치 (없는 경우)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Node.js 설치
brew install node@18

# MongoDB 설치 (Docker 사용 권장)
docker run -d -p 27017:27017 --name mongodb mongo

# Meilisearch 설치 (Docker 사용 권장)
docker run -d -p 7700:7700 --name meilisearch getmeili/meilisearch:v1.12.3

# Redis 설치 (선택, Docker 사용 권장)
docker run -d -p 6379:6379 --name redis redis:latest
```

### 2. 프로젝트 설정
```bash
# 의존성 설치
npm install

# 패키지 빌드
npm run build:packages
```

### 3. 환경 변수 설정
루트 디렉토리에 `.env` 파일 생성:

```bash
touch .env
```

`.env` 파일 내용:

```env
NODE_ENV=development
PORT=3080
HOST=localhost
MONGO_URI=mongodb://localhost:27017/LibreChat
MEILI_HOST=http://localhost:7700
MEILI_MASTER_KEY=change-this-master-key
JWT_SECRET=change-this-jwt-secret
JWT_REFRESH_SECRET=change-this-refresh-secret
SESSION_EXPIRES_IN=86400000
DOMAIN_SERVER=http://localhost:3080
DOMAIN_CLIENT=http://localhost:3080
```

**랜덤 문자열 생성:**
```bash
openssl rand -base64 32
```

### 4. 설정 파일 생성
```bash
cp librechat.example.yaml librechat.yaml
```

### 5. 실행
```bash
# 터미널 1: 백엔드
npm run backend:dev

# 터미널 2: 프론트엔드
npm run frontend:dev
```

### 6. 접속
브라우저에서: http://localhost:5173

### 7. 첫 사용자 생성
```bash
npm run create-user
```

---

## 🔧 문제 해결

| 문제 | 해결 방법 |
|------|----------|
| MongoDB 연결 오류 | `docker ps` 또는 `brew services list`로 확인 |
| 포트 충돌 | `lsof -i :3080`으로 확인 후 `kill -9 <PID>` |
| 모듈을 찾을 수 없음 | `npm run build:packages` 실행 |
| Meilisearch 오류 | `curl http://localhost:7700/health` 확인 |
| 권한 오류 | `sudo chown -R $(whoami) node_modules` |

---

## 📋 필수 체크리스트

- [ ] Homebrew 설치됨
- [ ] Node.js 18+ 설치됨
- [ ] MongoDB 실행 중
- [ ] Meilisearch 실행 중
- [ ] `.env` 파일 생성됨
- [ ] `librechat.yaml` 파일 생성됨
- [ ] `npm install` 완료
- [ ] `npm run build:packages` 완료
- [ ] 백엔드 실행 중
- [ ] 프론트엔드 실행 중

---

## 💡 Mac 특화 팁

1. **iTerm2 사용**: 기본 터미널보다 더 나은 경험
2. **Docker Desktop**: 모든 서비스를 한 번에 관리
3. **터미널 탭**: `Cmd + T`로 새 탭 열기
4. **프로세스 확인**: `lsof -i :PORT`로 포트 사용 확인

---

자세한 내용은 `MAC_SETUP.md`를 참조하세요.

