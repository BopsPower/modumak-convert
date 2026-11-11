# modumak-convert — 모두의음악 악보 변환 서버

모두의음악 새 사이트(modumak.co.kr)의 **모두 엔진** 악보를 만드는 서버. 모두플레이 변환 서버(BopsPower/moduplay `converter/`, 2026-11-11)에서 복사해 **따로 관리**(대표 11/11).
학원(모두플레이)과 계정·키·서버·Supabase를 같이 쓰지 않음.

## 하는 일 (2026-11-12부터 소리 먼저)
- 화면은 사이트가 브라우저에서 SVG로 그림(Verovio) → 변환 서버는 **소리(MP3) + 마디 시각(measures.json)** 을 먼저 만들어 `done`(AUDIO_FIRST, 기본 켬)
- 줄이 비면 PDF·MIDI(관리자 내려받기·유료 인쇄·실제 악기 소리용). 쪽 그림(PNG)은 안 만듦. `AUDIO_FIRST=0`이면 예전(모두플레이) 방식

## 예전 방식(AUDIO_FIRST=0)
- Supabase(modu-lms) `scores` 표에서 `convert_status = 'pending'`인 악보를 20초마다 가져옴
- 1단계: MuseScore 4 → PDF + 쪽 그림(PNG 150dpi) + 마디 위치표(`measures.json`) → `mark_converted`
- 2단계: MIDI · MusicXML · mscz · MP3
- 결과는 비공개 버킷 `scores`의 `out/<악보 id>/…` (볼 수 있는 회원만 읽음 — 31_songs.sql 보안 규칙)
- `hold` = 기다림(가져오기만 하고 변환 안 함)

## 설치 (GCP 프로젝트 modumak-score, VM modumak-convert, e2-small, 서울)
```
curl -fsSL https://raw.githubusercontent.com/BopsPower/modumak-convert/main/install.sh | sudo bash
sudo nano /etc/modumak-convert.env     # SUPABASE_SERVICE_KEY 는 대표가 직접 붙여넣기
sudo systemctl restart modumak-convert && sudo journalctl -u modumak-convert -f
```
비밀 키는 이 저장소에 절대 넣지 않음.

## 코드 갱신
VM 메타데이터 `startup-script`가 부팅할 때마다 `git pull` + 재시작 → GCP 콘솔에서 VM **재설정(Reset)** 만 누르면 최신 코드.
재설정 때 '그리는 중(converting)'이던 악보는 SQL로 `pending`으로 되돌림.
