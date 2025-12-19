# LibreChat Windows 빠른 시작 가이드

## ⚡ 빠른 설치 (5분)

### 1. 필수 소프트웨어 설치
```bash
# Node.js 설치 확인
node --version  # 18.x 이상 필요

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

### 4. 설정 파일 생성
```bash
copy librechat.example.yaml librechat.yaml
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
| MongoDB 연결 오류 | MongoDB가 실행 중인지 확인: `docker ps` |
| 포트 충돌 | `.env`에서 `PORT` 변경 |
| 모듈을 찾을 수 없음 | `npm run build:packages` 실행 |
| Meilisearch 오류 | `http://localhost:7700/health` 접속 확인 |

---

## 📋 필수 체크리스트

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

자세한 내용은 `WINDOWS_SETUP.md`를 참조하세요.

