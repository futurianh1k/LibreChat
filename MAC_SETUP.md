# LibreChat macOS 설치 및 실행 가이드

## 📋 프로젝트 개요

LibreChat은 오픈소스 ChatGPT 대안으로, 여러 AI 모델을 통합할 수 있는 웹 기반 채팅 플랫폼입니다.

**주요 구성 요소:**
- **Backend API**: Node.js/Express 서버 (`api/` 디렉토리)
- **Frontend Client**: React + Vite 클라이언트 (`client/` 디렉토리)
- **데이터베이스**: MongoDB (필수)
- **캐시**: Redis (선택, 권장)
- **검색 엔진**: Meilisearch (필수)

---

## 🔧 사전 요구사항

### 1. 필수 소프트웨어 설치

#### Homebrew 설치 (권장)
Homebrew는 macOS용 패키지 관리자입니다. 아직 설치하지 않았다면:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### Node.js 설치
```bash
# Homebrew를 사용한 설치 (권장)
brew install node@18

# 또는 최신 LTS 버전
brew install node

# 설치 확인
node --version
npm --version
```

**또는 공식 웹사이트에서 설치:**
- 다운로드: [https://nodejs.org/](https://nodejs.org/)
- macOS Installer (.pkg) 다운로드 및 설치

#### MongoDB 설치

**방법 1: Homebrew 사용 (권장)**
```bash
# MongoDB Community Edition 설치
brew tap mongodb/brew
brew install mongodb-community@7.0

# MongoDB 서비스 시작
brew services start mongodb-community@7.0

# 또는 수동 실행
mongod --config /opt/homebrew/etc/mongod.conf
```

**방법 2: Docker 사용 (더 간단)**
```bash
# Docker Desktop 설치: https://www.docker.com/products/docker-desktop
# 설치 후:
docker run -d -p 27017:27017 --name mongodb mongo
```

#### Meilisearch 설치

**방법 1: Homebrew 사용**
```bash
brew install meilisearch

# 실행 (마스터 키 설정)
meilisearch --master-key="your-master-key-here"
```

**방법 2: Docker 사용 (권장)**
```bash
docker run -d -p 7700:7700 --name meilisearch getmeili/meilisearch:v1.12.3
```

#### Redis 설치 (선택, 권장)

**방법 1: Homebrew 사용**
```bash
brew install redis

# Redis 서비스 시작
brew services start redis

# 또는 수동 실행
redis-server
```

**방법 2: Docker 사용**
```bash
docker run -d -p 6379:6379 --name redis redis:latest
```

---

## 🚀 설치 및 실행 단계

### 1단계: 프로젝트 의존성 설치

프로젝트 루트 디렉토리에서 실행:

```bash
# 루트 디렉토리로 이동
cd ~/path/to/LibreChat

# 모든 패키지 설치 (workspace 포함)
npm install
```

**참고**: 설치에는 시간이 걸릴 수 있습니다 (5-10분).

### 2단계: 환경 변수 설정

프로젝트 루트에 `.env` 파일을 생성합니다:

```bash
# .env 파일 생성
touch .env
```

`.env` 파일 내용 (최소 설정):

```env
# 서버 설정
NODE_ENV=development
PORT=3080
HOST=localhost

# MongoDB 설정 (필수)
MONGO_URI=mongodb://localhost:27017/LibreChat

# Meilisearch 설정 (필수)
MEILI_HOST=http://localhost:7700
MEILI_MASTER_KEY=your-master-key-here

# Redis 설정 (선택, 권장)
REDIS_URI=redis://localhost:6379

# JWT Secret (필수 - 랜덤 문자열 생성)
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production
JWT_REFRESH_SECRET=your-super-secret-refresh-key-change-this-in-production
JWT_EXPIRES_IN=24h
JWT_REFRESH_EXPIRES_IN=7d

# 세션 Secret (필수)
SESSION_EXPIRES_IN=86400000

# OpenAI API Key (선택 - OpenAI 사용 시)
OPENAI_API_KEY=sk-your-openai-api-key-here

# 기타 설정
DOMAIN_SERVER=http://localhost:3080
DOMAIN_CLIENT=http://localhost:3080
```

**중요**: 
- `JWT_SECRET`과 `JWT_REFRESH_SECRET`은 반드시 변경하세요
- `MEILI_MASTER_KEY`도 안전한 랜덤 문자열로 설정하세요

**랜덤 문자열 생성 방법:**
```bash
# 터미널에서 랜덤 문자열 생성
openssl rand -base64 32
```

### 3단계: LibreChat 설정 파일 생성

`librechat.example.yaml` 파일을 복사하여 `librechat.yaml` 생성:

```bash
cp librechat.example.yaml librechat.yaml
```

`librechat.yaml` 파일을 열어 필요한 설정을 수정합니다 (기본값으로도 작동합니다).

### 4단계: 데이터베이스 및 서비스 시작

#### MongoDB 시작
- Homebrew로 설치한 경우:
  ```bash
  brew services start mongodb-community@7.0
  ```
- Docker 사용 시:
  ```bash
  docker start mongodb
  # 또는 처음 실행 시
  docker run -d -p 27017:27017 --name mongodb mongo
  ```

#### Meilisearch 시작
- Homebrew로 설치한 경우:
  ```bash
  meilisearch --master-key="your-master-key-here" --env=development
  ```
- Docker 사용 시:
  ```bash
  docker start meilisearch
  # 또는 처음 실행 시
  docker run -d -p 7700:7700 --name meilisearch getmeili/meilisearch:v1.12.3
  ```

#### Redis 시작 (설치한 경우)
- Homebrew로 설치한 경우:
  ```bash
  brew services start redis
  ```
- Docker 사용 시:
  ```bash
  docker start redis
  # 또는 처음 실행 시
  docker run -d -p 6379:6379 --name redis redis:latest
  ```

**서비스 상태 확인:**
```bash
# Homebrew 서비스 확인
brew services list

# Docker 컨테이너 확인
docker ps
```

### 5단계: 패키지 빌드

프로젝트의 내부 패키지들을 먼저 빌드해야 합니다:

```bash
# 루트 디렉토리에서
npm run build:packages
```

### 6단계: 개발 모드로 실행

#### 옵션 1: 백엔드와 프론트엔드를 별도 터미널에서 실행

**터미널 1 - 백엔드 서버:**
```bash
npm run backend:dev
```

**터미널 2 - 프론트엔드 개발 서버:**
```bash
npm run frontend:dev
```

#### 옵션 2: 프로덕션 모드로 실행

**프론트엔드 빌드:**
```bash
npm run frontend
```

**백엔드 시작:**
```bash
npm run backend
```

### 7단계: 애플리케이션 접속

브라우저에서 다음 주소로 접속:
- **개발 모드**: http://localhost:5173 (Vite 기본 포트)
- **프로덕션 모드**: http://localhost:3080

---

## 👤 첫 사용자 생성

애플리케이션이 실행된 후, 첫 번째 관리자 사용자를 생성합니다:

```bash
npm run create-user
```

명령어 실행 시 다음 정보를 입력:
- 이메일
- 사용자명
- 비밀번호
- 역할 (admin 권한 부여)

---

## 🐛 문제 해결

### MongoDB 연결 오류
```
Error: Please define the MONGO_URI environment variable
```
**해결**: 
- `.env` 파일에 `MONGO_URI`가 올바르게 설정되어 있는지 확인
- MongoDB가 실행 중인지 확인:
  ```bash
  # Homebrew
  brew services list | grep mongodb
  
  # Docker
  docker ps | grep mongodb
  
  # 수동 확인
  mongosh --eval "db.version()"
  ```

### 포트 충돌
```
Error: listen EADDRINUSE: address already in use :::3080
```
**해결**: 
- 다른 애플리케이션이 3080 포트를 사용 중인지 확인:
  ```bash
  lsof -i :3080
  ```
- 프로세스 종료:
  ```bash
  kill -9 <PID>
  ```
- 또는 `.env` 파일에서 `PORT` 값을 다른 포트로 변경 (예: 3081)

### 패키지 빌드 오류
```
Error: Cannot find module '@librechat/...'
```
**해결**: 
```bash
npm run build:packages
```

### Meilisearch 연결 오류
**해결**: 
- Meilisearch가 실행 중인지 확인:
  ```bash
  curl http://localhost:7700/health
  ```
- `.env` 파일의 `MEILI_HOST`와 `MEILI_MASTER_KEY` 확인

### 권한 문제
```
Error: EACCES: permission denied
```
**해결**: 
- `node_modules` 디렉토리 권한 문제일 수 있음:
  ```bash
  sudo chown -R $(whoami) node_modules
  ```
- 또는 `npm install`을 다시 실행

### Homebrew 설치 오류
**해결**: 
- Homebrew 업데이트:
  ```bash
  brew update
  ```
- 권한 문제 해결:
  ```bash
  sudo chown -R $(whoami) /opt/homebrew
  ```

### Apple Silicon (M1/M2/M3) Mac 특이사항
- 일부 네이티브 모듈이 Rosetta 2를 통해 실행될 수 있음
- 대부분의 패키지는 Apple Silicon을 지원하지만, 문제가 있다면:
  ```bash
  # Rosetta 2 설치 (필요한 경우)
  softwareupdate --install-rosetta
  ```

---

## 📝 유용한 명령어

```bash
# 사용자 관리
npm run create-user          # 새 사용자 생성
npm run list-users           # 사용자 목록 보기
npm run reset-password        # 비밀번호 재설정
npm run delete-user          # 사용자 삭제

# 잔액 관리 (토큰 잔액 기능 사용 시)
npm run add-balance          # 잔액 추가
npm run list-balances        # 잔액 목록

# 데이터베이스
npm run reset-meili-sync     # Meilisearch 동기화 재설정

# 개발
npm run lint                 # 코드 린트
npm run test:all             # 모든 테스트 실행

# 서비스 관리 (Homebrew)
brew services list           # 모든 서비스 상태 확인
brew services start <service> # 서비스 시작
brew services stop <service>  # 서비스 중지
brew services restart <service> # 서비스 재시작

# Docker 관리
docker ps                    # 실행 중인 컨테이너 확인
docker start <container>     # 컨테이너 시작
docker stop <container>      # 컨테이너 중지
docker logs <container>      # 컨테이너 로그 확인
```

---

## 🔐 보안 권장사항

1. **프로덕션 환경에서는**:
   - `.env` 파일의 모든 시크릿 키를 강력한 랜덤 문자열로 변경
   - MongoDB에 인증 설정
   - HTTPS 사용
   - 방화벽 설정

2. **환경 변수 보호**:
   - `.env` 파일을 `.gitignore`에 포함 (이미 포함되어 있어야 함)
   - 프로덕션에서는 환경 변수를 시스템 레벨에서 설정

3. **macOS 방화벽 설정**:
   - 시스템 설정 > 네트워크 > 방화벽에서 필요시 설정

---

## 📚 추가 리소스

- **공식 문서**: [https://docs.librechat.ai](https://docs.librechat.ai)
- **GitHub**: [https://github.com/danny-avila/LibreChat](https://github.com/danny-avila/LibreChat)
- **커뮤니티**: [Discord](https://discord.librechat.ai)

---

## ✅ 체크리스트

설치 완료 확인:

- [ ] Homebrew 설치 완료
- [ ] Node.js 설치 완료
- [ ] MongoDB 설치 및 실행 중
- [ ] Meilisearch 설치 및 실행 중
- [ ] Redis 설치 및 실행 중 (선택)
- [ ] `.env` 파일 생성 및 설정 완료
- [ ] `librechat.yaml` 파일 생성 완료
- [ ] `npm install` 완료
- [ ] `npm run build:packages` 완료
- [ ] 백엔드 서버 실행 성공
- [ ] 프론트엔드 서버 실행 성공
- [ ] 브라우저에서 접속 성공
- [ ] 첫 사용자 생성 완료

---

## 💡 Mac 특화 팁

1. **iTerm2 사용**: 기본 터미널 대신 [iTerm2](https://iterm2.com/)를 사용하면 더 나은 개발 경험을 제공합니다.

2. **Docker Desktop**: 모든 서비스를 Docker로 실행하면 관리가 더 쉬워집니다:
   ```bash
   # docker-compose.yml 사용 (있는 경우)
   docker-compose up -d
   ```

3. **VS Code 확장 프로그램**: 
   - ESLint
   - Prettier
   - MongoDB for VS Code
   - Docker

4. **성능 최적화**: 
   - Redis를 사용하면 캐싱으로 성능이 향상됩니다
   - 프로덕션 빌드를 사용하면 더 빠릅니다

5. **터미널 탭 관리**: 
   - `Cmd + T`: 새 탭 열기
   - 여러 터미널 탭을 사용하여 백엔드와 프론트엔드를 분리하여 실행

6. **환경 변수 관리**: 
   - `.env` 파일 대신 `~/.zshrc` 또는 `~/.bash_profile`에 환경 변수를 설정할 수도 있습니다:
     ```bash
     export MONGO_URI="mongodb://localhost:27017/LibreChat"
     ```

7. **프로세스 관리**: 
   - `htop` 또는 `Activity Monitor`로 프로세스 모니터링
   - `lsof -i :PORT`로 포트 사용 확인

---

## 🎯 빠른 시작 스크립트

편의를 위해 다음 스크립트를 `start-dev.sh`로 저장할 수 있습니다:

```bash
#!/bin/bash

# LibreChat 개발 서버 시작 스크립트

echo "🚀 LibreChat 개발 서버 시작 중..."

# 서비스 확인
echo "📦 서비스 상태 확인 중..."
brew services list | grep -E "mongodb|redis" || docker ps | grep -E "mongodb|redis|meilisearch"

# 패키지 빌드 확인
if [ ! -d "packages/api/dist" ]; then
    echo "📦 패키지 빌드 중..."
    npm run build:packages
fi

# 백엔드 시작 (백그라운드)
echo "🔧 백엔드 서버 시작 중..."
npm run backend:dev &

# 잠시 대기
sleep 3

# 프론트엔드 시작
echo "🎨 프론트엔드 서버 시작 중..."
npm run frontend:dev
```

스크립트 실행 권한 부여:
```bash
chmod +x start-dev.sh
./start-dev.sh
```

---

이 가이드를 따라하시면 macOS에서 LibreChat을 성공적으로 실행할 수 있습니다!

