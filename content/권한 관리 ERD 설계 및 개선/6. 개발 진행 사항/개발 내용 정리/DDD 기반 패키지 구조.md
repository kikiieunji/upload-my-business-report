
# Permission Module DDD Refactoring Plan (Final)

이 문서는 사용자가 제공한 **`permission` 패키지의 DDD 폴더 구조**를 기반으로, **기존** **GroupFunction의 역할을 Permission으로 대체**하는 구현 계획입니다.

---
## 📂 DDD 폴더 구조 정의

제공해주신 이미지를 기반으로 아래와 같이 패키지를 구성하여 기능을 구현합니다. **핵심: 모든 권한/기능 관련 로직은 `domain/permission` 패키지 안에서 해결합니다.**

com.kikii.kikib.domain.permission

├── application

│   ├── annotation

│   │   └── RequirePermission.java

│   ├── aspect

│   │   └── PermissionAspect.java

│   ├── dto

│   │   └── PermissionDTO.java  (New: 클라이언트 반환용)

│   └── PermissionService.java  (New: 권한 조회 로직 담당)

├── domain

│   ├── entity

│   │   ├── Permission.java

│   │   ├── Role.java

│   │   ├── RolePermission.java

│   │   ├── UdpRole.java

│   │   └── ...

│   └── repository

│       ├── PermissionRepository.java

│       ├── RolePermissionRepository.java

│       ├── UdpRoleRepository.java

│       └── ...

├── infrastructure

│   └── persistence

│       └── ... (Impl 및 JpaRepository)

└── interfaces

    ├── controller

    │   └── PermissionController.java (New: API 진입점)

    └── GlobalExceptionHandler.java

---
