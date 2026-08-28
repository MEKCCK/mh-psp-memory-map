# 몬스터헌터 PSP 시리즈 메모리 맵 (MHF·MHF2·MHFU·MHP3RD)

PSP 몬헌 시리즈(MHF/MHF2/MHFU/MHP3RD, HD 포함)의 PSP 가상 메모리 주소 DB: 몬스터 구조체, 퀘스트 상태, 플레이어, 아이템, 렌더/카메라, 모드 로더 파이프라인.

> 모든 주소는 **PSP 가상 주소**（실기와 PPSSPP 동일）. 에뮬 프로세스 메모리를 외부 도구로 읽으려면 호스트 베이스 주소가 필요하며, 실기 치트 DB는 보통 "RAM 오프셋" 표기（절대 주소 = 오프셋 + `0x08800000`）.

## 내용

| 파일 | 설명 |
|---|---|
| [메모리 맵](docs/en/memory-map.md) | 4개 게임 전체 표＋주소 해설＋출처 태그 |
| [발굴 가이드](docs/en/digging-guide.md) | 새 주소 찾는 법（5 경로＋검증 체크리스트） |
| [기여하기](docs/en/contributing.md) | PR 제출／공개 주소 제공 방법 |
| [LANGUAGES.md](LANGUAGES.md) | 번역 현황과 참여 방법 |

## 빠른 사용법

| 상황 | 사용할 주소 |
|---|---|
| PPSSPP 내장 코드 / 메모리 편집기 | 절대 주소（예: `0x09B4C244`） |
| 실기 치트 DB "오프셋" | 먼저 `0x08800000` 더하기 |
| 외부 도구 (CE/Python) | 호스트 베이스(`Memory::base`)도 필요 |

## 출처와 감사

- Alexander-Lancellott / [MH-HP-Overlay-For-PSP-Emulator](https://github.com/Alexander-Lancellott/MH-HP-Overlay-For-PSP-Emulator) (Python)
- Kurogami2134: [mhp3reload](https://github.com/Kurogami2134/mhp3reload) · [p3rdml_modman](https://github.com/Kurogami2134/p3rdml_modman) · [mhp3rd_monster_hp_bar](https://github.com/Kurogami2134/mhp3rd_monster_hp_bar) · [mhp3rd_dmg_numbers](https://github.com/Kurogami2134/mhp3rd_dmg_numbers) · [p3rd_sharpness_indicator](https://github.com/Kurogami2134/p3rd_sharpness_indicator) · [p3rd_item_sets](https://github.com/Kurogami2134/p3rd_item_sets)

## 관련 프로젝트
- - PPSSPP 内蔵 MH HP オーバーレイ（本表を利用するエミュ内蔵版）: https://github.com/MEKCCK/ppsspp


## 라이선스
GPL-3.0. 데이터는 위 원본 프로젝트 소유이며, 본 저장소는 정리·주석·색인만 수행.

---
*한국어* · [English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Русский](README.ru.md) · [Français](README.fr.md) · [Deutsch](README.de.md)
