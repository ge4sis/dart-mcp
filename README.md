# DART-MCP: 재무 분석을 위한 MCP 확장 프로그램

DART API를 활용한 재무 분석 MCP(Model-assisted Capability Package)입니다. VS Code, Claude Desktop, Cursor 등을 이용하여 상장 기업의 재무 데이터를 쉽게 분석하고 시각화할 수 있습니다.

## 가능한 것 / 불가능한 것

### 가능한 것 (O)
- 주요 재무 분석
- 상세 재무 분석
- 기업의 사업부별 매출
- AI Tool을 이용한 시각화
- 재무지표를 활용한 벨류에이션 (DCF 등)

### 불가능한 것 (X)
- 주가 및 시가총액 제공
- 해외기업 분석
- AI Tool 무료 사용량 이상의 사용
- 100% 정확한 정보

**제공하는 투자 정보는 실제와 다를 수 있고 투자 책임은 투자한 본인에게 있습니다.**

## 사용 예시

### 재무 데이터 분석
```
파마리서치의 2023, 2024년 매출액, 영업이익 추이 분기별로 그래프로 보여줘. 그리고 매출비중이 어떻게 되는지 알려줘. 영업이익이나 매출액 변동 이유도 분석해줘.
```

### 기업 비교 분석
```
카카오와 네이버 2024년 수익성지표를 비교해서 분기별로 보여주고, 각 기업들은 어떤 사업부가 성장을 이끌지 알려줘.
```

### 재무 위험 평가
```
한국전력의 최근 부채상황을 조사하고, 상세하게 어떤 부분이 문제인지 분석해줘.
```

## 사전 준비

### DART API 키 발급
1. [DART 오픈API](https://opendart.fss.or.kr) 웹사이트에 접속
2. 회원가입 및 로그인
3. [인증키 신청/관리] - [오픈API 이용 신청] 메뉴 클릭
4. 이용정보 입력 후 신청
5. [인증키 신청/관리] - [오픈API 이용현황] 메뉴에서 발급된 인증키 확인

### MCP를 지원하는 Client 앱 설치
1. [Claude 데스크톱 앱](https://claude.ai/desktop) 다운로드
2. [VS Code](https://code.visualstudio.com/) 다운로드
3. [Cursor](https://cursor.sh/) 다운로드
4. 계정 가입 및 로그인

## 설치 방법

사용 환경에 따라 가장 편한 방법을 선택하세요.

### 방법 1: 실행 파일(EXE) 사용 (가장 쉬움, 윈도우 추천)
Python이나 기타 도구 설치가 필요 없습니다.
1. [Releases 페이지](https://github.com/acidburn44/dart-mcp/releases)에서 최신 `dart-mcp.exe`를 다운로드합니다.
2. 다운로드한 파일을 원하는 폴더(예: `C:\Util\dart-mcp.exe`)에 저장합니다.
3. **MCP 클라이언트 설정 (앱별 상세 가이드)**

   #### A. Claude Desktop
   1. **설정 > 개발자 > 설정 편집** 메뉴를 클릭합니다. (`claude_desktop_config.json` 파일이 열립니다)
   2. 아래 **[공통 설정 코드]**를 `mcpServers` 항목 안에 추가합니다.

   #### B. VS Code
   VS Code에서 MCP를 사용하려면 호환되는 확장 프로그램(예: Cline 등)이 필요할 수 있습니다.
   1. `Ctrl` + `Shift` + `P` (맥은 `Cmd` + `Shift` + `P`)를 눌러 명령어 창을 엽니다.
   2. **"Preferences: Open User Settings (JSON)"** (기본 설정: 사용자 설정 열기 (JSON))을 입력하고 선택합니다.
   3. 파일의 가장 마지막 중괄호 `}` 바로 위에 쉼표(`,`)를 찍고, **[공통 설정 코드]**를 추가합니다.
      (※ 이미 `mcpServers` 항목이 있다면 그 안에 내용만 추가하세요)

   #### [공통 설정 코드]
   ```json
   "mcpServers": {
     "dart-mcp": {
       "command": "C:\\Util\\dart-mcp.exe",
       "env": {
         "DART_API_KEY": "발급받은_API_키_입력"
       }
     }
   }
   ```
4. 설정 파일을 저장하고 Client 앱을 재시작합니다.

### 방법 2: PyPI로 설치 (Python 사용자)
Python이 설치된 환경이라면 `pip`로 쉽게 설치할 수 있습니다.
1. 터미널에서 설치:
   ```bash
   pip install dart-mcp-kor
   ```
2. **MCP 클라이언트 설정 추가**
   - 설정 파일을 여는 방법은 **[방법 1]**의 **A(Claude Desktop)** 또는 **B(VS Code)** 설명을 참고하세요.
   - 단, 설정 코드는 아래 내용을 사용해야 합니다 (`command`가 다릅니다).

   ```json
   "mcpServers": {
     "dart-mcp": {
       "command": "dart-mcp",
       "env": {
         "DART_API_KEY": "발급받은_API_키_입력"
       }
     }
   }
   ```

### 방법 3: Claude Code (CLI) 설정
터미널 기반 AI 어시스턴트인 Claude Code를 사용하는 경우, Python 환경이나 `uv`를 통해 직접 M되어 설정할 수 있습니다.

**설정 방법 A: CLI 명령어 사용**
Claude Code 내에서 `/mcp add` 명령을 사용하여 서버를 추가합니다. `uvx`를 권장합니다.

```bash
# Claude Code를 실행 중인 터미널 프롬프트에서:

# 1. uv를 사용하는 경우 (가장 강력하고 빠른 방법)
> /mcp add uvx --from dart-mcp-kor dart-mcp

# 2. Python 환경에 pip로 설치 후 사용하는 경우
> /mcp add python -m dart_mcp
```
환경 변수는 Claude Code 실행 전에 터미널에서 `export DART_API_KEY="발급받은_API_키_입력"`으로 설정해야 합니다.

**설정 방법 B: `claude.json` 파일 사용 (권장)**
프로젝트 루트 디렉토리나 글로벌 설정 파일인 `claude.json`을 직접 수정하여 서버와 환경 변수를 한 번에 설정할 수 있습니다.
 
```json
{
  "mcpServers": {
    "dart-mcp": {
      "command": "uvx",
      "args": ["--from", "dart-mcp-kor", "dart-mcp"],
      "env": {
        "DART_API_KEY": "발급받은_API_키_입력"
      }
    }
  }
}
```
※ `uvx` 대신 `python`을 사용할 경우 `command`를 `"python"`, `args`를 `["-m", "dart_mcp"]`로 지정하세요.

### 방법 4: 소스 코드 직접 실행 (개발용)
직접 코드를 수정하거나 기여하고 싶은 경우 사용합니다. (`uv` 또는 `python` 필요)
1. 저장소를 클론하거나 다운로드합니다.
2. 프로젝트 폴더로 이동하여 의존성을 설치하고 실행합니다.
   ```json
   "dart-mcp": {
     "command": "uv",
     "args": ["--directory", "/Users/{사용자이름}/Downloads/dart-mcp", "run", "dart.py"],
     "env": {
       "DART_API_KEY": "{DART_API_KEY}"
     }
   }
   ```

## 사용시 주의사항
- 기업명은 공식적으로 상장된 이름으로 제공해야 합니다.
- 코스피, 코스닥 종목만 조사 가능합니다.
- 주가나 시가총액과 같은 실시간 정보들은 앞으로 연동할 계획입니다.