# GitOps TODO 애플리케이션

> Spring Boot + React 기반 완전 자동화된 GitOps CI/CD 파이프라인

개발자가 코드를 푸시하면 자동으로 빌드, 배포까지 완료되는 현대적인 DevOps 파이프라인을 홈랩 Kubernetes 환경에 구축한 프로젝트입니다.

## 🏗️ 시스템 아키텍처

```
개발자 코드 푸시 → GitHub Actions → GitHub Container Registry → ArgoCD → Kubernetes 클러스터
    (Spring Boot)         (CI 빌드)         (이미지 저장)         (CD 배포)    (홈서버 운영)
    (React)
```

### 📂 리포지토리 구조

**애플리케이션 리포지토리**
```
1. todo app backend

├── .github
│   └── workflows
│       └── build.yml
├── .gitignore
├── build.gradle
├── Dockerfile
├── README.md
├── settings.gradle
└── src/
```

```
2. todo app frontend

├── .github
│   └── workflows
│       └── build.yml
├── Dockerfile
├── README.md
├── public/
├── src/
├── .gitignore
├── eslint.config.js
├── index.html
├── package-lock.json
├── package.json
├── postcss.config.js
├── .env.development
├── .env.production
├── tailwind.config.js
└── vite.config.js
```

**설정 리포지토리** (argocd-deploy)
```
├── back                   # Backend K8s 리소스
│   ├── configmap.yaml
│   ├── deployment.yaml
│   ├── sealeddbsecret.yaml
│   └── service.yaml
├── front                  # Frontend K8s 리소스
│   ├── deployment.yaml
│   └── service.yaml
├── README.md
└── shared                 # 공통 리소스
    ├── ingress.yaml
    └── sealedtlssecret.yaml
```

## 🔄 CI/CD 파이프라인 동작 과정

### 1. 코드 개발 & 푸시
```bash
# 개발자가 코드 수정 후 푸시
git add .
git commit -m "새로운 기능 추가"
git push origin main
```

### 2. GitHub Actions 자동 실행
- **백엔드**: Gradle 빌드 → Docker 이미지 생성 → GitHub Container Registry 푸시
- **프론트엔드**: npm 빌드 → Docker 이미지 생성 → GitHub Container Registry 푸시
- **이미지 태그**: commit SHA 사용으로 고유성 보장

### 3. ArgoCD 자동 배포
- 설정 리포지토리의 Kubernetes 매니페스트 감지
- 홈서버 Kubernetes 클러스터에 자동 배포
- Rolling Update로 무중단 배포
- Health Check로 배포 성공 여부 확인

## 🛠️ 기술 스택

### 애플리케이션
- **백엔드**: Spring Boot 3.x, Gradle, Java 17
- **프론트엔드**: React 19.1.0, JavaScript ES6+
- **데이터베이스**: PostgreSQL

### DevOps 도구
- **CI**: GitHub Actions
- **CD**: ArgoCD (GitOps)
- **Container Registry**: GitHub Container Registry (ghcr.io)
- **Orchestration**: Kubernetes
- **Ingress**: NGINX Ingress Controller

### 홈랩 인프라
- **k3s 클러스터**: 3노드 (마스터 1, 워커 1)
- **네트워킹**: Flannel CNI


## 🚀 주요 특징

### GitOps 패턴 적용
- **선언적 배포**: Git을 통한 인프라 상태 관리
- **자동 동기화**: 코드 변경 시 자동 배포
- **롤백 지원**: Git 히스토리 기반 즉시 롤백
- **가시성**: 모든 변경사항이 Git에 기록

### 완전 자동화
- **빌드 자동화**: 코드 푸시 → 이미지 빌드
- **배포 자동화**: 이미지 업데이트 → 클러스터 배포
- **설정 관리**: ConfigMap/Secret 기반 환경 설정
- **네트워크 구성**: Ingress 기반 트래픽 라우팅

## 🔧 핵심 구현 사항

### GitHub Actions Workflow
```yaml
# 백엔드/프론트엔드 병렬 빌드
# Docker 멀티스테이지 빌드로 이미지 최적화
# GitHub Container Registry 자동 푸시
# Commit SHA 기반 이미지 태깅
```

### ArgoCD Application
```yaml
# Git 리포지토리 모니터링
# 자동 동기화 정책 설정
```

### Kubernetes 리소스
```yaml
# Deployment: Rolling Update 전략
# Service: ClusterIP/LoadBalancer 타입
# Ingress: 도메인 기반 라우팅
# ConfigMap/Secret: 환경별 설정 분리
```

## 🏠 홈랩 환경 구성

### 하드웨어 사양
- **마스터 노드**: 2core, 2GB RAM
- **워커 노드 1**: 2core, 2GB RAM  

### 설치된 컴포넌트
- **Kubernetes**: v1.32+
- **ArgoCD**: v3.0.6+

## 💡 학습 성과

### DevOps 역량
- ✅ GitOps 패턴 이해 및 구현
- ✅ 컨테이너 오케스트레이션 실무 경험
- ✅ CI/CD 파이프라인 설계 및 구축
- ✅ Infrastructure as Code 적용

### 운영 경험
- ✅ Kubernetes 클러스터 구축 및 관리
- ✅ 서비스 디스커버리 및 로드밸런싱
- ✅ 보안 설정 및 Secret 관리
- ✅ 모니터링 및 트러블슈팅

### 문제 해결 경험
- ✅ 컨테이너 이미지 최적화
- ✅ 네트워크 정책 및 Ingress 설정

## 🚀 실행 방법

### 전제 조건
- Kubernetes 클러스터 (v1.32+)
- ArgoCD 설치 및 설정
- GitHub 계정 및 Container Registry 접근 권한

### 빠른 시작
1. **리포지토리 복제**
   ```bash
   git clone https://github.com/hjmoons/todo-web
   git clone https://github.com/hjmoons/todo-was
   git clone https://github.com/hjmoons/argocd-deploy
   ```

2. **GitHub Actions 설정**
   - Repository Secrets에 GITHUB_TOKEN 추가

3. **ArgoCD Git Repository 설정**

4. **애플리케이션 접속**
   ```bash
   # /etc/hosts 설정 또는 DNS 구성
   # IP는 워커 노드 아이피 확인
   echo "192.168.1.100 hyojong.com" >> /etc/hosts
   # 브라우저에서 http://hyojong.com 접속
   ```

---

*💡 이 프로젝트는 현대적인 DevOps 방법론과 GitOps 패턴을 실습하며 홈랩 Kubernetes 환경에서 직접 구축한 실무 경험을 담고 있습니다.*
