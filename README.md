# 🛒 E-commerce 12th Street - GitOps Manifests

이 리포지토리는 **E-commerce 12th Street** 프로젝트의 Kubernetes 배포 설정(Manifest)을 관리하는 GitOps 저장소입니다.  

**ArgoCD**를 통해 Kubernetes 클러스터와 상태를 동기화합니다.

## 📂 프로젝트 구조

이 저장소는 **Directory 기반** 구조로 되어 있어, Helm이나 Kustomize 없이 순수 YAML 파일을 관리합니다.

```text
.
├── _apps/
│   └── appset-plain.yaml       # ArgoCD ApplicationSet 설정 (모든 프로젝트 자동 등록)
│
└── projects/                   # 실제 배포될 서비스들의 Manifest 모음
    ├── tomcat-board/           # [게시판 서비스] Deployment, Service, Secret, HPA
    ├── tomcat-member/          # [회원 서비스] Deployment, Service, Secret, HPA
    ├── ingress-nginx/          # [게이트웨이] Nginx Controller, Ingress Rules
    └── common/                 # [공통 리소스] MetalLB, 유지보수용 Job 등
```

## 배포 방법 (Initial Setup)

ArgoCD가 설치된 Kubernetes 클러스터에서 다음 명령어를 한 번만 실행하면, projects 폴더 내의 모든 애플리케이션이 자동으로 배포됩니다.

```
# ArgoCD ApplicationSet 적용
kubectl apply -f _apps/appset-plain.yaml
```

적용 후 ArgoCD UI에서 tomcat-board, tomcat-member 등의 애플리케이션이 생성되고 Synced 상태가 되는지 확인하세요.

## 배포 업데이트 가이드 (GitOps Workflow)

새로운 버전의 애플리케이션을 배포하려면 다음 절차를 따르세요.

1. 이미지 빌드: 소스 코드 저장소에서 개발 및 테스트 후 Docker 이미지를 빌드합니다. (예: v1.1)
2. Manifest 수정: 이 저장소의 해당 서비스 폴더(예: projects/tomcat-board)로 이동합니다.
3. 태그 변경: deployment.yaml 파일에서 image 태그를 수정합니다.

```
# projects/tomcat-board/board-deploy.yaml
containers:
  - name: tomcat-board-container
    image: [820313036770.dkr.ecr.ap-northeast-2.amazonaws.com/board-service:v1.1](https://820313036770.dkr.ecr.ap-northeast-2.amazonaws.com/board-service:v1.1)  # <-- 여기 수정
```

