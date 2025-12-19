# LibreChat Windows 실행 가이드 - 요약

## 🚀 빠른 시작 (3단계)

### 1. 필수 소프트웨어 설치
```bash
# Node.js 18+ 설치 확인
node --version

# Docker로 서비스 실행 (권장)
docker run -d -p 27017:27017 --name mongodb mongo
docker run -d -p 7700:7700 --name meilisearch getmeili/meilisearch:v1.12.3
docker run -d -p 6379:6379 --name redis redis:latest
```

### 2. 프로젝트 설정
```bash
npm install
npm run build:packages
```

### 3. 환경 설정 및 실행
```bash
# .env 파일 생성 (루트 디렉토리)
# 내용은 아래 참조

# librechat.yaml 생성
copy librechat.example.yaml librechat.yaml

# 실행 (2개 터미널)
# 터미널 1:
npm run backend:dev

# 터미널 2:
npm run frontend:dev
```

접속: http://localhost:5173

---

## 📝 필수 .env 파일 내용

루트 디렉토리에 `.env` 파일을 생성하고 다음 내용을 추가:

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

**중요**: `MEILI_MASTER_KEY`, `JWT_SECRET`, `JWT_REFRESH_SECRET`은 반드시 강력한 랜덤 문자열로 변경하세요!

---

## 📚 상세 가이드

- **전체 설치 가이드**: `WINDOWS_SETUP.md` 참조
- **빠른 참조**: `WINDOWS_QUICK_START.md` 참조
- **코드 리뷰**: `CODE_REVIEW.md` 참조

---

## ✅ 체크리스트

- [ ] Node.js 18+ 설치
- [ ] MongoDB 실행 중
- [ ] Meilisearch 실행 중
- [ ] `.env` 파일 생성 및 설정
- [ ] `librechat.yaml` 파일 생성
- [ ] `npm install` 완료
- [ ] `npm run build:packages` 완료
- [ ] 백엔드 실행 중
- [ ] 프론트엔드 실행 중

---

## 🆘 문제 해결

| 문제 | 해결 |
|------|------|
| MongoDB 연결 오류 | `docker ps`로 MongoDB 실행 확인 |
| 포트 충돌 | `.env`에서 `PORT` 변경 |
| 모듈을 찾을 수 없음 | `npm run build:packages` 실행 |
| Meilisearch 오류 | `http://localhost:7700/health` 확인 |

---

## 📞 도움말

- 공식 문서: https://docs.librechat.ai
- GitHub: https://github.com/danny-avila/LibreChat
- Discord: https://discord.librechat.ai

