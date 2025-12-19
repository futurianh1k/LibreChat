# Windows에서 Docker Compose로 LibreChat 한 번에 구동하기

## 🎯 개요

Docker Compose를 사용하면 LibreChat과 필요한 모든 서비스(MongoDB, Meilisearch, Redis 등)를 **한 번의 명령어**로 실행할 수 있습니다.

**장점:**
- ✅ 모든 서비스를 자동으로 설정
- ✅ 환경 설정이 간단함
- ✅ 서비스 간 네트워크 자동 구성
- ✅ 데이터 영속성 보장
- ✅ 쉽게 시작/중지 가능

---

## 📋 사전 요구사항

### 1. Docker Desktop 설치

1. **Docker Desktop 다운로드**
   - 공식 사이트: https://www.docker.com/products/docker-desktop
   - Windows용 설치 파일 다운로드

2. **설치**
   - 다운로드한 `.exe` 파일 실행
   - 설치 마법사 따라하기
   - 설치 후 **재시작 필요**

3. **설치 확인**
   ```powershell
   docker --version
   docker-compose --version
   ```

4. **Docker Desktop 실행**
   - 시작 메뉴에서 "Docker Desktop" 실행
   - 시스템 트레이에 Docker 아이콘이 나타나면 준비 완료

**중요**: Docker Desktop이 실행 중이어야 합니다!

---

## 🚀 설치 및 실행 단계

### 1단계: 프로젝트 준비

```powershell
# 프로젝트 디렉토리로 이동
cd E:\cursorworks\LibreChat

# Git 저장소 클론 (아직 안 했다면)
# git clone https://github.com/danny-avila/LibreChat.git
# cd LibreChat
```

### 2단계: 환경 변수 파일 생성

프로젝트 루트에 `.env` 파일을 생성합니다:

```powershell
# PowerShell에서 .env 파일 생성
New-Item -Path .env -ItemType File -Force
```

`.env` 파일에 다음 내용을 추가하세요:

```env
# ============================================
# 필수 설정
# ============================================
NODE_ENV=development
PORT=3080
HOST=0.0.0.0

# MongoDB 설정
MONGO_URI=mongodb://mongodb:27017/LibreChat

# Meilisearch 설정
MEILI_HOST=http://meilisearch:7700
MEILI_MASTER_KEY=your-master-key-change-this

# JWT 설정 (반드시 변경하세요!)
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production
JWT_REFRESH_SECRET=your-super-secret-refresh-key-change-this-in-production
JWT_EXPIRES_IN=24h
JWT_REFRESH_EXPIRES_IN=7d

# 세션 설정
SESSION_EXPIRES_IN=86400000

# 도메인 설정
DOMAIN_SERVER=http://localhost:3080
DOMAIN_CLIENT=http://localhost:3080

# ============================================
# Docker Compose 전용 설정
# ============================================
# Windows에서 UID/GID 설정 (일반적으로 1000)
UID=1000
GID=1000

# RAG API 포트 (선택)
RAG_PORT=8000

# ============================================
# 선택적 설정
# ============================================
# Redis 설정 (선택, 권장)
# REDIS_URI=redis://redis:6379

# OpenAI API Key (OpenAI 사용 시)
# OPENAI_API_KEY=sk-your-openai-api-key-here
```

**중요 사항:**
- `MEILI_MASTER_KEY`, `JWT_SECRET`, `JWT_REFRESH_SECRET`은 **반드시 강력한 랜덤 문자열로 변경**하세요
- Windows에서 UID/GID는 보통 1000을 사용합니다

**랜덤 문자열 생성 방법:**
```powershell
# PowerShell에서 랜덤 문자열 생성
-join ((48..57) + (65..90) + (97..122) | Get-Random -Count 32 | ForEach-Object {[char]$_})
```

### 3단계: LibreChat 설정 파일 생성

```powershell
# librechat.yaml 파일 생성
Copy-Item librechat.example.yaml librechat.yaml
```

기본 설정으로도 작동하지만, 필요시 `librechat.yaml`을 수정할 수 있습니다.

### 4단계: Docker Compose로 실행

#### 방법 1: 개발 모드 (docker-compose.yml 사용)

```powershell
# 모든 서비스 시작
docker-compose up -d

# 또는 로그를 보면서 시작
docker-compose up
```

**이 방법의 특징:**
- 개발용 이미지 사용
- 포트: 3080 (API 서버)
- 모든 서비스가 자동으로 시작됨

#### 방법 2: 프로덕션 모드 (deploy-compose.yml 사용)

```powershell
# 프로덕션 모드로 시작
docker-compose -f deploy-compose.yml up -d
```

**이 방법의 특징:**
- 프로덕션용 이미지 사용
- 포트: 80 (Nginx를 통한 접근)
- API와 클라이언트가 분리됨

### 5단계: 서비스 상태 확인

```powershell
# 실행 중인 컨테이너 확인
docker ps

# 로그 확인
docker-compose logs -f

# 특정 서비스 로그만 보기
docker-compose logs -f api
docker-compose logs -f mongodb
```

**예상되는 컨테이너:**
- `LibreChat` (또는 `LibreChat-API`) - 메인 애플리케이션
- `chat-mongodb` - MongoDB 데이터베이스
- `chat-meilisearch` - 검색 엔진
- `vectordb` - 벡터 데이터베이스 (RAG용)
- `rag_api` - RAG API 서버

### 6단계: 애플리케이션 접속

브라우저에서 다음 주소로 접속:

- **개발 모드**: http://localhost:3080
- **프로덕션 모드**: http://localhost

### 7단계: 첫 사용자 생성

애플리케이션이 실행된 후, 첫 번째 관리자 사용자를 생성합니다:

```powershell
# 컨테이너 내에서 명령어 실행
docker-compose exec api npm run create-user
```

또는 Windows에서 직접 실행 (Node.js가 설치되어 있다면):

```powershell
npm run create-user
```

---

## 🛠️ 유용한 Docker Compose 명령어

### 서비스 관리

```powershell
# 모든 서비스 시작
docker-compose up -d

# 모든 서비스 중지
docker-compose down

# 모든 서비스 중지 및 볼륨 삭제 (데이터 삭제)
docker-compose down -v

# 특정 서비스만 재시작
docker-compose restart api

# 서비스 상태 확인
docker-compose ps

# 서비스 로그 보기
docker-compose logs -f

# 특정 서비스 로그만 보기
docker-compose logs -f mongodb
```

### 데이터 관리

```powershell
# MongoDB에 접속
docker-compose exec mongodb mongosh

# 데이터베이스 백업
docker-compose exec mongodb mongodump --out /data/backup

# 볼륨 확인
docker volume ls
```

### 문제 해결

```powershell
# 컨테이너 내부로 들어가기
docker-compose exec api sh

# 컨테이너 재빌드 (이미지 변경 시)
docker-compose up -d --build

# 로그에서 에러 확인
docker-compose logs api | Select-String -Pattern "error" -Context 5
```

---

## 🐛 문제 해결

### 1. Docker Desktop이 실행되지 않음

**증상:**
```
error during connect: This error may indicate that the docker daemon is not running
```

**해결:**
- Docker Desktop을 시작 메뉴에서 실행
- 시스템 트레이에 Docker 아이콘이 있는지 확인
- Docker Desktop이 완전히 시작될 때까지 대기 (몇 분 소요)

### 2. 포트 충돌

**증상:**
```
Error: bind: address already in use
```

**해결:**
```powershell
# 포트 사용 확인
netstat -ano | findstr :3080

# 프로세스 종료 (PID 확인 후)
taskkill /PID <PID> /F

# 또는 .env 파일에서 PORT 변경
```

### 3. 권한 문제

**증상:**
```
Permission denied
```

**해결:**
- PowerShell을 **관리자 권한**으로 실행
- Docker Desktop 설정에서 파일 공유 권한 확인

### 4. 컨테이너가 시작되지 않음

**해결:**
```powershell
# 로그 확인
docker-compose logs api

# 컨테이너 재시작
docker-compose restart api

# 완전히 재생성
docker-compose down
docker-compose up -d
```

### 5. MongoDB 연결 오류

**해결:**
```powershell
# MongoDB 컨테이너 상태 확인
docker-compose ps mongodb

# MongoDB 로그 확인
docker-compose logs mongodb

# MongoDB 재시작
docker-compose restart mongodb
```

### 6. .env 파일을 찾을 수 없음

**해결:**
- `.env` 파일이 프로젝트 루트에 있는지 확인
- 파일 이름이 정확히 `.env`인지 확인 (`.env.txt` 아님)
- PowerShell에서 확인:
  ```powershell
  Get-ChildItem -Force | Where-Object {$_.Name -eq ".env"}
  ```

### 7. Windows 경로 문제

**해결:**
- Docker Desktop 설정에서 WSL 2 백엔드 사용 권장
- 또는 Windows 컨테이너 모드 사용

---

## 📊 서비스 구조 이해

### docker-compose.yml 구조

```
LibreChat (Docker Compose)
├── api (메인 애플리케이션)
│   ├── 포트: 3080
│   ├── 의존성: mongodb, rag_api
│   └── 볼륨: .env, images, uploads, logs
├── mongodb (데이터베이스)
│   ├── 포트: 27017 (내부)
│   └── 볼륨: ./data-node
├── meilisearch (검색 엔진)
│   ├── 포트: 7700 (내부)
│   └── 볼륨: ./meili_data_v1.12
├── vectordb (벡터 DB)
│   └── 볼륨: pgdata2
└── rag_api (RAG API)
    └── 의존성: vectordb
```

### 네트워크

모든 서비스는 같은 Docker 네트워크에 있어서 서비스 이름으로 서로 접근할 수 있습니다:
- `mongodb:27017` - MongoDB 접근
- `meilisearch:7700` - Meilisearch 접근
- `redis:6379` - Redis 접근 (설정한 경우)

---

## 🔄 업데이트 방법

### 애플리케이션 업데이트

```powershell
# 최신 이미지 가져오기
docker-compose pull

# 서비스 재시작
docker-compose up -d

# 또는 한 번에
docker-compose pull && docker-compose up -d
```

### 데이터 백업

```powershell
# MongoDB 데이터 백업
docker-compose exec mongodb mongodump --archive=/data/backup.archive

# 백업 복원
docker-compose exec mongodb mongorestore --archive=/data/backup.archive
```

---

## 🔐 보안 권장사항

1. **프로덕션 환경:**
   - `.env` 파일의 모든 시크릿 키를 강력한 랜덤 문자열로 변경
   - MongoDB에 인증 설정
   - HTTPS 사용 (리버스 프록시 설정)

2. **포트 노출:**
   - 개발 환경에서만 포트를 외부에 노출
   - 프로덕션에서는 리버스 프록시 사용

3. **볼륨 권한:**
   - 민감한 데이터가 있는 볼륨의 권한 확인

---

## 📝 체크리스트

설치 완료 확인:

- [ ] Docker Desktop 설치 및 실행 중
- [ ] `.env` 파일 생성 및 설정 완료
- [ ] `librechat.yaml` 파일 생성 완료
- [ ] `docker-compose up -d` 실행 성공
- [ ] `docker ps`로 모든 컨테이너 실행 확인
- [ ] 브라우저에서 http://localhost:3080 접속 성공
- [ ] 첫 사용자 생성 완료

---

## 💡 추가 팁

### 1. Docker Compose 오버라이드 사용

로컬 개발 환경만의 설정을 추가하려면 `docker-compose.override.yml` 파일을 생성:

```yaml
# docker-compose.override.yml
services:
  api:
    volumes:
      - ./local-config:/app/config
```

이 파일은 자동으로 로드되며, 기본 설정을 오버라이드합니다.

### 2. 리소스 모니터링

```powershell
# 컨테이너 리소스 사용량 확인
docker stats

# 특정 컨테이너만
docker stats LibreChat
```

### 3. 개발 모드 vs 프로덕션 모드

- **개발 모드** (`docker-compose.yml`): 
  - 빠른 개발 및 테스트
  - 핫 리로드 지원
  - 디버깅 용이

- **프로덕션 모드** (`deploy-compose.yml`):
  - 최적화된 빌드
  - Nginx를 통한 정적 파일 서빙
  - 더 나은 성능

### 4. 로그 관리

```powershell
# 로그 파일 크기 제한 (docker-compose.yml에 추가)
logging:
  driver: "json-file"
  options:
    max-size: "10m"
    max-file: "3"
```

---

## 🎯 요약

Docker Compose를 사용하면:

1. ✅ **한 번의 명령어**로 모든 서비스 시작
2. ✅ **자동 네트워크 구성**
3. ✅ **데이터 영속성 보장**
4. ✅ **쉬운 관리 및 업데이트**
5. ✅ **일관된 개발 환경**

**시작 명령어:**
```powershell
docker-compose up -d
```

**중지 명령어:**
```powershell
docker-compose down
```

이제 LibreChat을 Windows에서 쉽게 실행할 수 있습니다! 🚀

