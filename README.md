# 쿠버네티스 설정 파일

클라우드 시스템 최종 프로젝트 
CodeSphere 애플리케이션 아키텍처를 위한 
쿠버네티스 yaml파일들과 Docker Compose 설정을 포함하고 있습니다.

## 디렉토리 구조
```bash
├── ingress.yaml          # Ingress 설정
├── docker-compose.yaml
├── nginx.conf            # Nginx 설정 파일
├── frontend/
│   ├── fe-deploy.yaml    # 프론트엔드 배포 설정
│   └── fe-service.yaml   # 프론트엔드 서비스 설정
├── backend/
│   ├── be-deploy.yaml    # 백엔드 배포 설정
│   └── be-service.yaml   # 백엔드 서비스 설정
├── postgres/
│   ├── db-deploy.yaml    # 데이터베이스 배포 설정
│   ├── db-service.yaml   # 데이터베이스 서비스 설정
│   ├── db-pv.yaml        # 영구 볼륨 설정
│   └── db-pvc.yaml       # 영구 볼륨 클레임 설정
├── sa.yaml               # 서비스 계정 설정
├── cluster_role.yaml     # 클러스터 롤 설정 (네임스페이스/파드 관리 권한)
└── cluster_rb.yaml       # 클러스터 롤 바인딩 설정
```

## 구성 요소

### Ingress
ingress.yaml 파일은 외부 트래픽을 클러스터 내부 서비스로 라우팅하는 설정을 포함합니다:

/api 경로의 요청을 백엔드 서비스(포트 8000)로 전달
루트 경로(/)의 요청을 프론트엔드 서비스(포트 3000)로 전달
kubelabx 네임스페이스에서 동작

### Docker Compose
루트 디렉토리의 `docker-compose.yaml` 파일은 로컬에서 연결을 테스트하기 위해 사용한 파일입니다.
### Nginx Configuration
nginx.conf 파일은 Nginx 웹 서버의 설정을 포함하며, 프론트엔드와 백엔드 서비스로의 트래픽을 적절히 라우팅합니다.

### frontend
프론트엔드 디렉토리는 프론트엔드 애플리케이션을 위한 쿠버네티스 매니페스트를 포함합니다:
- `fe-deploy.yaml`: 프론트엔드 파드의 배포 설정을 정의
- `fe-service.yaml`: 프론트엔드 배포를 다른 서비스나 외부 접근에 노출

### backend
백엔드 디렉토리는 백엔드 애플리케이션을 위한 쿠버네티스 매니페스트를 포함합니다:
- `be-deploy.yaml`: 백엔드 파드의 배포 설정을 정의
- `be-service.yaml`: 백엔드 배포를 다른 서비스에 노출

### postgres
postgres 디렉토리는 데이터베이스 설정을 포함합니다:
- `db-deploy.yaml`: PostgreSQL 배포 설정을 정의
- `db-service.yaml`: PostgreSQL 서비스를 노출
- `db-pv.yaml`: 데이터베이스 저장소를 위한 영구 볼륨 구성
- `db-pvc.yaml`: 데이터베이스 사용을 위한 영구 볼륨 클레임

### RBAC 파일
쿠버네티스 API 연동을 위한 RBAC 설정 파일들:
- `sa.yaml`: ServiceAccount 설정
- `cluster_role.yaml`: 네임스페이스와 파드 관리를 위한 ClusterRole 설정
- `cluster_rb.yaml`: ServiceAccount와 ClusterRole을 연결하는 ClusterRoleBinding 설정

이 RBAC 설정들은 백엔드에서 쿠버네티스 API를 사용하여 네임스페이스와 파드를 관리하기 위해 필요합니다.
참고: 이러한 RBAC 설정을 사용한 쿠버네티스 API 연동은 현재 작동하지 않습니다.

## 사용 방법

### 로컬 개발
로컬에서 애플리케이션을 실행하려면:
```bash
docker-compose up
```

### 쿠버네티스 배포
쿠버네티스에 구성 요소를 배포하려면:

1. PostgreSQL 배포:
```bash
kubectl apply -f postgres/
```

2. 백엔드 배포:
```bash
kubectl apply -f backend/
```

3. 프론트엔드 배포:
```bash
kubectl apply -f frontend/
```

4. RBAC 설정 적용:
```bash
kubectl apply -f sa.yaml
kubectl apply -f cluster_role.yaml
kubectl apply -f cluster_rb.yaml
```
