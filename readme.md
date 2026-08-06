# TOTEM × Asurada — ZMK build config

이 저장소는 **[TOTEM](https://github.com/GEIGEIGEIST/totem) 스플릿 키보드 + Asurada
동글 디스플레이 + Adept 트랙볼** 조합의 펌웨어를 빌드하는 **ZMK 설정(빌드 레시피)**
입니다. · This is the **ZMK config** that builds firmware for a **TOTEM split
keyboard + an Asurada dongle display + an Adept trackball**.

화면 펌웨어·3D는 여기 없고 별도 모듈 **[diagbyte/asurada_dongle](https://github.com/diagbyte/asurada_dongle)**
에 있습니다. 이 저장소는 `config/west.yml`로 그 모듈을 끌어와, 내 **키맵**·하드웨어와
합쳐 GitHub Actions에서 `.uf2` 펌웨어로 컴파일합니다. (이 repo엔 `.c` 코드가 없습니다 —
키맵 + 빌드 설정뿐.)

**[한국어](#한국어) · [English](#english)**

---

## 한국어

### 구성 (동글 토폴로지, 전부 `xiao_ble//zmk`)

| 빌드 타깃 | 역할 |
|---|---|
| `totem_dongle asurada_adapter` | 중앙(central) + GC9A01 원형 디스플레이 = **동글** |
| `totem_left` / `totem_right` | 키보드 반쪽 (주변장치) |
| `adept_trackball` | PMW3610 포인팅 주변장치 |
| `settings_reset` | 옛 BLE 본딩 초기화용 (토폴로지 바꿀 때) |

트랙볼 없이 **키보드 전용**으로 쓰려면 동글을 `-DCONFIG_ASURADA_TRACKBALL=n` 변형으로
빌드하세요 (`build.yaml`에 `totem_dongle_notrackball` 타깃으로 이미 포함).

동글에는 **DS3231 RTC + SHT45 온습도 센서**가 터치와 같은 I2C 버스(D4/D5)에 붙습니다
(주소 `0x68` / `0x44`, 새 핀 없음). 배선과 주의사항은 모듈 쪽
[`docs/wiring-clock-climate.md`](https://github.com/diagbyte/asurada_dongle/blob/main/docs/wiring-clock-climate.md).

### 키맵 레이어

| # | 이름 | 들어가는 법 |
|---|---|---|
| 0 | `BASE` (QWERTY) | 기본. 한글 두벌식은 이걸로 |
| 1 | `CMK-DH` | ADJ의 `R` 자리 `&tog CMKDH` (영문 전용) |
| 2 | `NAVI` | 왼쪽 엄지 `&lt NAV TAB` |
| 3 | `SYM` | 오른쪽 엄지 `&lt SYM ESC` |
| 4 | `ADJ` | NAV 또는 SYM 상태에서 `&mo ADJ` |
| 5–6 | `TVP 1` / `TVP 2` | 콤보(11 12 13) → `&tog TVP1` |
| 7–9 | `SCRLM` / `SCRLK` / `SNIPE` | 트랙볼 버튼(스크롤·스나이프) |
| 10 | `TIME` | **ADJ + `T`** → 동글 시계 설정 화면 |

### 동글 시계 맞추기

`TIME` 레이어의 키는 전부 `&none`이라 **편집 중 PC로 글자가 가지 않습니다.** 화면 편집기는
키코드가 아니라 **키 위치**를 읽습니다:

| 키(BASE 기준 자리) | 동작 |
|---|---|
| `E` / `D` | 값 **+1 / −1** (누르고 있으면 연속) |
| `S` / `F` | **이전 / 다음 필드** (년 → 월 → 일 → 시 → 분) |
| 오른쪽 엄지 `RET` | **저장** 후 BASE 복귀 |

저장 시 초가 0이 되므로 **다음 분으로 맞춰 놓고 정각에 저장**하면 초까지 정확합니다.
연도는 화면에 안 보여도 맞춰야 합니다 — 요일을 날짜에서 계산하기 때문입니다.
설정 값은 `config/totem_dongle.conf`의 `CONFIG_ASURADA_TIME_SET_LAYER=10`.

### 빌드 & 플래시

1. **키맵 수정** → `config/totem.keymap` (키코드는 [ZMK 문서](https://zmk.dev/docs/codes/))
2. `git push` → GitHub **Actions** → 빌드된 `firmware.zip` 다운로드
3. **처음이거나 토폴로지를 바꿨다면**, 각 XIAO에 `settings_reset`을 먼저 플래시해 옛
   본딩을 지웁니다 (리셋 2번 → USB 저장소로 뜨면 `.uf2` 드래그).
4. 각 부품에 해당 `.uf2`를 플래시:
   - 동글: `totem_dongle …uf2`
   - 좌/우 반쪽: `totem_left …uf2` / `totem_right …uf2`
   - 트랙볼: `adept_trackball …uf2`

### 어디에 뭐가 있나

- **키맵**: `config/totem.keymap`
- **트랙볼 센서·BLE 튜닝**: `config/boards/shields/adept_trackball/adept_trackball.conf`
- **동글/반쪽 설정**: `config/totem_dongle.conf`, `config/boards/shields/totem/`
- **모듈 참조**: `config/west.yml` (`asurada_dongle` @ `main`)
- **화면 펌웨어·3D는 여기 아님** → **[asurada_dongle](https://github.com/diagbyte/asurada_dongle)** 모듈

---

## English

### Topology (all `xiao_ble//zmk`)

| Build target | Role |
|---|---|
| `totem_dongle asurada_adapter` | central + GC9A01 round display = the **dongle** |
| `totem_left` / `totem_right` | keyboard halves (peripherals) |
| `adept_trackball` | PMW3610 pointing peripheral |
| `settings_reset` | wipe old BLE bonds when changing topology |

For a **keyboard-only** build (no trackball), build the dongle with the
`-DCONFIG_ASURADA_TRACKBALL=n` variant (already in `build.yaml` as
`totem_dongle_notrackball`).

The dongle also carries a **DS3231 RTC and an SHT45** on the touch panel's I2C
bus (D4/D5, addresses `0x68` and `0x44`, no extra pins). Wiring and cautions:
[`docs/wiring-clock-climate.md`](https://github.com/diagbyte/asurada_dongle/blob/main/docs/wiring-clock-climate.md)
in the module.

### Keymap layers

| # | Name | How to reach it |
|---|---|---|
| 0 | `BASE` (QWERTY) | default — also what Korean 두벌식 input needs |
| 1 | `CMK-DH` | `&tog CMKDH` on ADJ (`R` position), English only |
| 2 | `NAVI` | left thumb `&lt NAV TAB` |
| 3 | `SYM` | right thumb `&lt SYM ESC` |
| 4 | `ADJ` | `&mo ADJ` while on NAV or SYM |
| 5–6 | `TVP 1` / `TVP 2` | combo (11 12 13) → `&tog TVP1` |
| 7–9 | `SCRLM` / `SCRLK` / `SNIPE` | trackball buttons (scroll / snipe) |
| 10 | `TIME` | **ADJ + `T`** → the dongle's clock editor |

### Setting the dongle clock

Every key on the `TIME` layer is `&none`, so **nothing is typed into the host
while editing**. The on-screen editor reads key *positions*, not keycodes:

| Key (BASE position) | Action |
|---|---|
| `E` / `D` | value **+1 / −1** (hold to repeat) |
| `S` / `F` | **previous / next field** (year → month → day → hour → minute) |
| right thumb `RET` | **save** and return to BASE |

Saving zeroes the seconds, so dial in the *next* minute and save on the tick to
be second-accurate. Set the year even though it is not displayed — the weekday is
computed from the full date. Configured by
`CONFIG_ASURADA_TIME_SET_LAYER=10` in `config/totem_dongle.conf`.

### Build & flash

1. **Edit the keymap** → `config/totem.keymap` (keycodes on the [ZMK docs](https://zmk.dev/docs/codes/)).
2. `git push` → GitHub **Actions** → download the built `firmware.zip`.
3. **First time / after changing topology**, flash `settings_reset` to each XIAO
   first to clear old bonds (double-tap reset → it mounts as USB storage → drag the `.uf2`).
4. Flash the matching `.uf2` to each part:
   - dongle: `totem_dongle …uf2`
   - halves: `totem_left …uf2` / `totem_right …uf2`
   - trackball: `adept_trackball …uf2`

### Where things live

- **Keymap**: `config/totem.keymap`
- **Trackball sensor / BLE tuning**: `config/boards/shields/adept_trackball/adept_trackball.conf`
- **Dongle / half configs**: `config/totem_dongle.conf`, `config/boards/shields/totem/`
- **Module reference**: `config/west.yml` (`asurada_dongle` @ `main`)
- **Display firmware + 3D are NOT here** → the **[asurada_dongle](https://github.com/diagbyte/asurada_dongle)** module

---

## Credits

- **TOTEM** keyboard hardware & the original ZMK config template this started from:
  [GEIGEIGEIST/totem](https://github.com/GEIGEIGEIST/totem). TOTEM is a 38-key
  column-staggered split for the Seeed XIAO BLE.
- **Asurada dongle display** module: [diagbyte/asurada_dongle](https://github.com/diagbyte/asurada_dongle)
  (based on / heavily derived from Prospector).
- **Trackball**: an Adept (AdeptBLE) trackball converted to a ZMK split pointing peripheral.
