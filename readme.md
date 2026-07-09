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
