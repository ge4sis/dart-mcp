# 배포 편의성 개선 스펙 (Distribution Improvement Spec)

## 배경
현재 `dart-mcp` 프로젝트는 소스 코드를 직접 다운로드하고 `uv`를 통해 실행해야 하는 구조로, 일반 사용자가 접근하기 어렵습니다. 이를 개선하기 위해 **PyPI 패키지 배포**와 **실행 파일(EXE) 배포** 방식을 도입하고자 합니다.

## 목표
1. **PyPI 배포**: `pip install dart-mcp` 명령어로 쉽게 설치 및 실행 가능하도록 함.
2. **EXE 배포**: Python 환경 설정 없이 바로 실행 가능한 단일 실행 파일 제공.
3. **다양한 MCP 클라이언트 지원**: Claude Desktop뿐만 아니라 VS Code Copilot Chat, Cursor 등 `stdio` 통신을 지원하는 모든 MCP 클라이언트에서 쉽게 사용할 수 있도록 **범용적인 설정 가이드** 제공.

## 변경 계획

### 1. 프로젝트 구조 개편 (Refactoring)
패키징 표준에 맞게 파일 구조를 변경합니다.

**변경 전:**
```
Running...
dart-mcp/
├── dart.py
├── pyproject.toml
└── ...
```

**변경 후:**
```
dart-mcp/
├── src/
│   └── dart_mcp/
│       ├── __init__.py      # 버전 정보 및 패키지 초기화
│       ├── main.py          # 기존 dart.py 내용 이동
│       └── __main__.py      # 'python -m dart_mcp' 실행 지원
├── pyproject.toml           # 빌드 시스템 설정 추가
├── README.md
└── ...
```

### 2. PyPI 배포 설정 (`pyproject.toml`)
`hatchling` 또는 `setuptools`를 빌드 백엔드로 설정하고, CLI 진입점(`entry_points`)을 정의합니다.

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "dart-mcp"
version = "0.1.0"
scripts = { "dart-mcp" = "dart_mcp.main:main" }
```

### 3. PyInstaller 실행 파일 생성 (`dart-mcp.spec`)
`PyInstaller`를 사용하여 단일 실행 파일(`onefile`)을 생성하는 스펙 파일을 작성합니다.
`uv` 환경 의존성 문제를 해결하고, 필요한 메타데이터를 포함합니다.

Build Command:
```bash
pyinstaller --name dart-mcp --onefile --hidden-import=uvicorn --hidden-import=fastapi src/dart_mcp/main.py
```

### 4. 사용자 가이드 (`README.md`) 수정
설치 방법을 다음 세 가지로 구분하여 안내합니다.
1. **실행 파일 사용 (권장)**: EXE 다운로드 및 설정.
2. **PyPI 설치**: `pip install dart-mcp` 후 `dart-mcp` 명령어 사용.
3. **소스 코드 실행**: 개발자용 (기존 방식 유지).

## 실행 단계
1. `src/dart_mcp` 디렉토리 생성 및 `dart.py` 이동/리네임.
2. `pyproject.toml` 업데이트.
3. 로컬 빌드 및 테스트 (`pip install .`).
4. PyInstaller 빌드 테스트 (`pyinstaller ...`).
5. GitHub Actions 워크플로우 업데이트: 기존 EXE 릴리즈에 추가로 OIDC 기반 PyPI 자동 배포(`pypa/gh-action-pypi-publish`) 적용 (완료).

## PyPI 자동 배포 (Trusted Publishing) 설정 가이드 (프로젝트 소유자용)
GitHub Action을 통한 PyPI 배포가 작동하려면 PyPI 사이트에서 아래 설정을 진행해야 합니다.
1. [PyPI](https://pypi.org/)에 로그인합니다.
2. Account settings -> Publishing 항목으로 이동합니다.
3. "Add a pending publisher"를 클릭하여 새 OIDC publisher를 추가합니다.
   - **PyPI Project Name**: `dart-mcp`
   - **Owner**: `acidburn44`
   - **Repository name**: `dart-mcp`
   - **Workflow name**: `release.yml`
4. 설정을 저장하면, 이후 `v*` 태그 푸시 시 자동으로 PyPI에 휠(wheel)과 소스 배포판(sdist)이 업로드됩니다.
