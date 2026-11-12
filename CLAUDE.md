# 🧱 흔들탑 (Wobble Tower)

친구들과 하는 물리 블록 쌓기 파티 게임. 트리키 타워 같은 **장르**의 오리지널 게임이다 (특정 게임을 그대로 베끼지 않기: 이름·캐릭터·아트·주문 이름 금지).
이 폴더는 머신러닝 실습(상위 폴더)과 무관하다. 여기서는 Claude가 코드를 직접 작성해도 된다 (상위 CLAUDE.md의 "코드 대신 작성 금지"는 ML 실습에만 해당).

## 🔗 링크
| 용도 | 주소 |
|---|---|
| 친구용 (계정 불필요) | https://sesin0523-oss.github.io/wobble-tower/ |
| GitHub 저장소 (공개) | https://github.com/sesin0523-oss/wobble-tower |
| Claude artifact (Claude 로그인 + 공유 필요) | https://claude.ai/artifact/WwdSQGSCadedUU1Zkw4dJk |

⚠️ Claude 링크에서 만든 방과 GitHub 링크에서 만든 방은 **서로 절대 못 만난다.** 친구와는 GitHub 링크로 통일.

## 📁 파일 / 배포 흐름
- `index.html` 한 파일이 게임 전체 (Matter.js 0.19.0 cdnjs, mqtt 5.10.1 jsdelivr).
- 이 폴더가 git 저장소 (`main` → GitHub Pages, 반영 1~2분, 브라우저 캐시 최대 10분 → 친구는 Ctrl+F5).
- git 신원: 저장소 로컬 설정 `sesin0523-oss` / `sesin0523-oss@users.noreply.github.com` (개인 이메일 쓰지 않기).
- artifact 원본은 doctype/head/body 없는 형식이다. `index.html`은 그 원본에 `<!doctype html>…<body>` 껍데기를 씌운 것. 수정 시 **둘 다** 맞춰서: artifact 재게시(같은 URL에 `url` 지정) + `index.html` 커밋·푸시.
- 로컬 테스트: 상위 폴더 `.claude/launch.json`의 `wobble` 설정 (python http.server 8765, `--directory WobbleTower`).

## 🎮 게임 내용
- 모드: 🏁 레이스(20칸 결승선 넘기고 2초 버티기) / ❤️ 서바이벌(하트 3, 떨어뜨리면 -1, 점점 빨라짐)
- 한 컴퓨터 1~4명: P1 WASD+Q, P2 방향키+/, P3 IJKL+U, P4 숫자패드 8456+7
- 아이템(블록 4개마다): 🧊 얼음(상대 다음 블록 미끄러움) 🎈 뻥튀기(1.5배) 🌪️ 돌풍 ⚓ 닻(내 탑 위 3개 고정)
- 모든 플레이어에게 같은 블록 순서 (온라인은 방장이 seed 배포)

## 🌐 온라인 구조
- 게임 코드는 Claude `room` API 모양(`join(name) → presence / onPeers / leave`)에 맞춰져 있고, 연결 방식만 갈아끼운다:
  - artifact 안: `claude.use('room')` (capabilities `{room:{}}`)
  - GitHub 링크: `makeMqttRoomApi()` — 공개 브로커 `wss://broker.emqx.io:8084/mqtt` (실패 시 hivemq). 주제 `wobbletower/v1/wt-<코드>/<클라이언트id>`에 각자 상태를 retain 메시지로 올림, 나갈 때·Last Will로 비움, 12초 소식 없으면 정리.
- 각자 자기 탑 물리는 자기 컴퓨터에서 계산하고, 상태(블록 위치 압축, 초당 10번)를 presence로 보냄. 남의 탑은 받아서 보간 표시.
- 방장 = 가장 먼저 들어온 사람(`j` 시각). 방장이 presence `g`로 게임 시작, `r`로 결과 발표. 아이템 공격은 presence `fx` 목록(seq 번호).
- 화면 아래 회색 글씨 `버전 vN · 연결 방식` 으로 친구와 같은 버전인지 확인. 버전 올릴 때 `GAME_VERSION` 수정.

## 🕘 지난 문제와 해결 (2026-11-12)
1. Claude `room`은 로그인한 사람만 접속 가능 → 계정 없는 친구용으로 GitHub Pages 배포.
2. PeerJS(P2P)로 했더니 다른 집 친구가 "방을 찾을 수 없어요" → 재시도 버그(같은 Peer로 재시도하면 peer-unavailable이 다시 안 옴) 고침 → 그래도 실패.
3. **v6에서 MQTT 서버 중계 방식으로 교체.** 탭 두 개로는 성공 확인. **서로 다른 네트워크에서 실제 친구와는 아직 미확인** ← 다음에 결과 물어보기.

## ➡️ 다음 할 것
- 친구와 v6로 실제 접속되는지 확인 (안 되면 빨간 오류 문구 + 회색 버전 줄 받기)
- 아이디어: 게임패드 지원, 새 아이템, 모바일 터치 조작
