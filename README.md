# modumak-convert — 모두의음악 악보 변환 서버

모두의음악 새 사이트(modumak.co.kr)의 **모두 엔진** 악보를 만드는 서버. 모두플레이 변환 서버(BopsPower/moduplay `converter/`, 2026-11-11)에서 복사해 **따로 관리**(대표 11/11).
학원(모두플레이)과 계정·키·서버·Supabase를 같이 쓰지 않음.

## 하는 일
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
