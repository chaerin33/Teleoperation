# Vision-Based Hand Tracking Robot Teleoperation

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-TBD-lightgrey)

RGB-D 카메라 기반 실시간 핸드 트래킹 로봇 원격 조작 시스템입니다. Intel RealSense 카메라로 촬영한 손 자세를 MediaPipe 랜드마크 검출과 3D 좌표 변환을 통해 처리하고, 노이즈 필터링을 거쳐 로봇 팔 관절 명령으로 출력합니다.

---

## 시스템 구조

<img width="1970" height="879" alt="piperine" src="https://github.com/user-attachments/assets/ebff80f0-7a3e-45a6-8742-82ab9c3c626a" />

**동작 단계:**
1. **안정화 단계** — 시작 시, 손 랜드마크 위치가 임계값 이내로 안정화될 때까지 대기한 후 명령을 수신합니다.
2. **트래킹 단계** — 필터링된 자세를 로봇 팔에 지속적으로 출력합니다.

---

## 요구 사항

- Python 3.8+
- Intel RealSense SDK 2.0 (`pyrealsense2`)
- MediaPipe (`mediapipe`)
- NumPy (`numpy`)
- OpenCV (`opencv-python`)
- SciPy (`scipy`)
- Rainbow Robotics SDK (`rbpodo`)

---

## 설치

```bash
git clone https://[https://github.com/chaerin33/Teleoperation_RGB-D.git]
mkdir Robot
cd Robot
pip install pyrealsense2 mediapipe numpy opencv-python scipy rbpodo
```

> **참고:** Intel RealSense SDK는 별도 설치가 필요합니다.
> [librealsense 설치 가이드](https://github.com/IntelRealSense/librealsense)를 참고하세요.
>  Rainbow Robotics SDK는 별도 설치가 필요합니다.
> [librealsense 설치 가이드](https://github.com/RainbowRobotics/rbpodo)를 참고하세요.

---

## 실행

```bash
python hand_tracking_control_deg_final.py
```

종료하려면 `q`를 누르세요.

---

## 설정

`hand_tracking_control_deg_final.py` 상단에서 주요 파라미터를 수정할 수 있습니다:

| 파라미터 | 설명 |
|---------|------|
| `DEPTH_KERNEL_SIZE` | 깊이 샘플링 윈도우 크기 (기본값: 7×7) |
| `OUTLIER_THRESHOLD_MM` | 이상치 제거 거리 임계값 (mm) |
| `EMA_ALPHA` | EMA 필터 스무딩 계수 (0~1, 낮을수록 부드러움) |
| `STAB_THRESHOLD` | 안정화 단계의 랜드마크 이동 임계값 |
| `STAB_FRAMES` | 트래킹 시작 전 안정 상태를 유지해야 하는 연속 프레임 수 |
| `R` | 카메라 → 로봇 좌표 변환을 위한 3×3 회전 행렬 |
| `t` | 카메라 → 로봇 좌표 변환을 위한 평행 이동 벡터 (mm) |

> ⚠️ **주의:** `R`과 `t`는 설치 환경에 따라 달라집니다. 실제 사용 환경에서 반드시 재측정 후 업데이트해야 하며, 코드에 포함된 기본값을 그대로 사용할 수 없습니다.

---

## 출력 명세

시스템은 매 제어 주기(~30Hz)마다 아래 데이터를 출력합니다:

| 필드 | 타입 | 단위 | 설명 |
|-----|------|------|------|
| `joint_angles` | float[N] | 도 (°) | 로봇 팔 목표 관절 각도 |
| `tcp_position` | float[3] | mm | 로봇 베이스 프레임 기준 목표 TCP 위치 (x, y, z) |
| `tcp_orientation` | float[3] | 도 (°) | xyz 오일러 각도로 표현한 목표 TCP 방향 |
| `gripper` | int | — | 그리퍼 명령: `1` = Grab, `0` = Release |

> ⚠️ **주의:** 방향 출력은 xyz 오일러 각도로 표현됩니다. 연동 전에 로봇 팔이 요구하는 입력 컨벤션과 일치하는지 반드시 확인하세요.

---

## 라이선스

TBD
