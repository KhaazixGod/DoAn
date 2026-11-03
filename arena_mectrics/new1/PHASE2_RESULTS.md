# PHASE2_RESULTS — kết quả đánh giá trên arena (DEV)

Cập nhật: 2026-11-01T22:25+07:00. Nguồn: `reports/phase2_episode_metrics.csv`, `reports/phase2_summary_metrics.csv`, `results/raw/<run_id>/`.

## 1. Phương pháp nào thực sự đã chạy trên arena?
**Không có. 0 arena trials.**

## 2. Số trial thực tế và điều kiện
- Tổng trial trong `results/raw`: **0**.
- Blocker: `cl0/` và `dir1/` không tồn tại (xem `reports/MISSING_DEPENDENCIES.md`): runner `arena/run_arena.py`, platform, trial, methods đều import `cl0` ở cấp module; `arena/arrangements.py` import `dir1/make_arrangements` ở cấp module → không trial nào (kể cả NOOP/ORACLE) chạy được.

## 3. SR / clean SR / latency / bộ nhớ (method × cell × speed)

Không có dòng nào (0 arena trials).

## 4. Thất bại chính
Chưa có trial → chưa có phân bố nguyên nhân.

## 5. Phân loại kết quả: baseline / paper adaptation / component-only / offline

**Arena rollouts:** không có.

**Offline (KHÔNG phải kết quả arena; bảng riêng):**

| kiểm chứng offline P19 ACT (checkpoint 020000, pilot, original_method_port single-arm) | train ep | val ep (arrangement giữ lại) |
|---|---|---|
| MAE khớp (°) vs action chuyên gia ORACLE, teacher-forced | 1.24 | 3.10 |
| MAE gripper (0–100) | 1.16 | 1.66 |
| latency in-process warm p50/p95 ms (CUDA sync, gồm resize+chuẩn hoá) | 17.36 / 24.35 | 18.13 / 26.57 |
| cold first call ms | 864.8 | — |
| reset deterministic | True | |
| pre/post-processor cần thiết (MAE gọi thô → đúng) | 55.3° → 3.10° | |

- REMOTE qua `arena/remote_server.py` thật: round-trip p50/p95 **25.37 / 44.42 ms** (server step 19.12 / 29.11 ms, IPC ≈ 4.45 ms, khung 1280×720 ≈ 2.7 MB), 150 bước, action hữu hạn, MAE 5.03° (150 bước đầu val ep). `results/offline/p19_act_remote_roundtrip.json`.

- Val-loss L1 (eval mode, 11 tập giữ lại) 0.169 vs train 0.061 (`checkpoints/p19_act/run1_pilot/val_loss_020000.json`, `train_loss_evalmode_020000.json`). Loss thấp **không** là bằng chứng gắp thành công.
- Front-end ảnh tham chiếu (P05 SIFT/CNS, P07 DINOv2, P18 RAFT) trên ảnh render thật: xem `data/object_refs/_frontend_composite_smoke.json` và docs P05/P07/P18 — vật không texture → SIFT 0 match; DINO lệch 54 px; RAFT chỉ khớp gần pose demo.

## 6. Phương pháp còn blocked và đầu vào cần

| paper | fidelity | status | blocker |
|---|---|---|---|
| P01 | survey_only | not_applicable | survey: no method to run |
| P02 | survey_only | not_applicable | survey: no method to run |
| P03 | survey_only | not_applicable | survey: no method to run |
| P04 | faithful_reimplementation | blocked | missing code + weights (GitHub search 0, no statement in paper); eye-in-hand AprilTag goal-image servoing with |
| P05 | component_only | pending | eye-in-hand goal-image servo has no equivalent with one fixed camera; 6-DoF camera velocity vs 5-DoF arm |
| P06 | faithful_reimplementation | blocked | code not released (project page: v2 not released); no weights; training needs 6,852 GSO/OmniObject3D models in |
| P07 | component_only | pending | eye-in-hand goal-image IBVS has no equivalent with one fixed camera; license unspecified (repo has no LICENSE) |
| P08 | faithful_reimplementation | blocked | missing code + checkpoint; needs per-object keypoint observer trained with manual annotations + sim DAgger; ey |
| P09 | adapted_method | blocked | missing checkpoint (grasp evaluator must be trained on ACRONYM/ShapeNetSem in Isaac Gym); wrist D405 point clo |
| P10 | faithful_reimplementation | blocked | nothing released (code, value function, 2M-trajectory dataset = 6 GPU-days); needs cuRobo config for SO-101 |
| P11 | component_only | pending | tactile/force-torque + wrist camera required for the reactive contribution; released checkpoints are Flexiv-sp |
| P12 | component_only | blocked | paywalled full text; no code; no checkpoint; SMMA module unverifiable -> a plain YOLO11 detector must NOT be r |
| P13 | component_only | pending | dual-arm closed chain, eye-in-hand cameras, F/T; no code; no SR table |
| P14 | faithful_reimplementation | pending | no code; no quantitative metrics in paper; stereo object recognizer replaced by ideal depth |
| P15 | adapted_method | blocked | full text paywalled (method unverified beyond abstract); no code; dual-arm multi-camera |
| P16 | not_implemented | blocked | needs_arena_extension: two aerial manipulators with onboard cameras and a shared payload; full text inaccessib |
| P17 | component_only | pending | released code = wrist-camera Stages 2-3 only; no license; needs one in-arena demo per object |
| P18 | component_only | pending | eye-in-hand assumption in code; no license; RAFT/FlowNet2 weights fetched separately; gym-grasping dep not pub |
| P19 | original_method_port | pending | must train single-arm ACT (LeRobot act = acknowledged port) on arena demos; demos currently = author's 51 ORAC |
| P20 | adapted_method | blocked | missing code + checkpoint; needs gaze-labelled demos; ee-delta 10 Hz actions |
| P21 | not_implemented | blocked | needs_arena_extension: second arm (inter-arm encoder has no meaning on one arm; degenerates to ACT = P19); no  |
| P22 | not_implemented | blocked | needs_arena_extension: tactile sensor model + second arm; tactile-only RL policy, no camera input; PyBullet Ta |
| P23 | faithful_reimplementation | pending | code not released (stub repo); needs trained single-arm DP base (P-DP via LeRobot) + differentiable SO-101 FK  |
| P24 | adapted_method | blocked | code welded to RoboTwin + DP fork; needs Isaac rollout worker + hundreds of rollouts/iteration (compute); bima |
| P25 | component_only | pending | partial code (plugin only, no train/eval); no checkpoint; dual-arm 6-view embodiment |
| P26 | adapted_method | blocked | no code; bimanual manipulability formulation; real-only data |
| P27 | not_implemented | blocked | needs_arena_extension: nonholonomic mobile base + second arm + articulated door; no code |
| P28 | adapted_method | blocked | code not released (repo 404); no checkpoint; Siamese two-flow + VLM arm assignment is the contribution |
| P29 | original_method_port | pending | released checkpoint is bimanual PerAct2 -> training required; point cloud from ideal depth + intrinsics; EE-po |
| P30 | component_only | pending | requires wrist cameras (paper excludes third-person augmentation) + second arm for the coordination constraint |

**Đầu vào cần để mở khoá toàn bộ pipeline:** `Capstone/cl0/` + `Capstone/dir1/` (layout trong `reports/MISSING_DEPENDENCIES.md`), kèm commit/hash. Sau đó: `scripts/run_phase2_dev.ps1 -Stage smoke` rồi `-Stage dev`.

## Đếm
- Hồ sơ registry: 30/30 · baseline đã chạy trên arena: 0 · phương pháp registry đã có rollout arena: 0 · offline-checked: 1 (P19 ACT).
