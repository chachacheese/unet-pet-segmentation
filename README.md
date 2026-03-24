# 🐾 U-Net Semantic Segmentation — Oxford-IIIT Pet Dataset

PyTorch로 U-Net을 직접 구현하고, Oxford-IIIT Pet Dataset으로 시맨틱 세그멘테이션을 학습한 실습 프로젝트입니다.

> 📄 논문: [U-Net: Convolutional Networks for Biomedical Image Segmentation](https://arxiv.org/abs/1505.04597) (Ronneberger et al., MICCAI 2015)

---

## 📌 프로젝트 개요

반려동물 이미지를 픽셀 단위로 분류하는 세그멘테이션 모델을 구현합니다.

| 항목 | 내용 |
|------|------|
| 모델 | U-Net (직접 구현) |
| 데이터셋 | Oxford-IIIT Pet Dataset |
| 태스크 | Semantic Segmentation |
| 프레임워크 | PyTorch |
| 입력 크기 | 128 × 128 |
| 클래스 수 | 3 (배경 / 동물 / 윤곽선) |

---

## 🗂️ 주요 흐름
```
1. 환경 준비   →   2. 데이터 준비   →   3. 모델링 (블록 단위)   →   4. 모델링 (전체 U-Net)   →   5. 학습 & 평가
```

---

## 🏗️ 모델 구조
```
입력 (3, 128, 128)
│
inc   : DoubleConv(3 → 64)          ┐
down1 : Down(64 → 128)              │  Encoder
down2 : Down(128 → 256)             │  (skip connection 저장)
down3 : Down(256 → 512)             │
down4 : Down(512 → 1024)  Bottleneck┘
│
up1   : Up(1024 → 512)  ←── skip4  ┐
up2   : Up(512  → 256)  ←── skip3  │  Decoder
up3   : Up(256  → 128)  ←── skip2  │
up4   : Up(128  →  64)  ←── skip1  ┘
│
outc  : Conv2d(64 → num_classes, kernel=1)
│
출력 (3, 128, 128)
```

### 구성 블록

- **DoubleConv** : (Conv2d → BatchNorm → ReLU) × 2
- **Down** : MaxPool2d → DoubleConv (해상도 ½, 채널 2배)
- **Up** : ConvTranspose2d → Skip Concat → DoubleConv (해상도 2배, skip connection 결합)
- **OutConv** : Conv2d 1×1 (채널 → 클래스 수)

---

## 📦 데이터셋

**Oxford-IIIT Pet Dataset** — 37종 반려동물, 약 7,400장

| 분할 | 사용 |
|------|------|
| trainval | 학습 |
| test (절반) | 검증 (val) |
| test (절반) | 최종 평가 (test) |

마스크 클래스: `0` 배경 / `1` 동물 / `2` 윤곽선

---

## ⚙️ 학습 설정

| 항목 | 값 |
|------|-----|
| Batch Size | 8 |
| Epochs | 20 |
| Optimizer | Adam (lr=1e-3) |
| Loss | CrossEntropyLoss |
| 평가 지표 | mIoU (mean Intersection over Union) |

### 데이터 증강 (Augmentation)
- 수평 뒤집기 (hflip) — 50% 확률
- 수직 뒤집기 (vflip) — 50% 확률
- 90도 회전 (rotate) — 50% 확률

---

## 📊 평가 지표: IoU

$$\text{IoU} = \frac{\text{예측 영역} \cap \text{실제 영역}}{\text{예측 영역} \cup \text{실제 영역}}$$

픽셀 정확도(accuracy) 대신 IoU를 사용하는 이유 — 클래스 불균형 환경에서 배경만 맞춰도 높은 accuracy가 나오는 함정을 방지하기 위해 클래스별 IoU를 따로 계산한 뒤 평균(mIoU)을 사용합니다.

---

## 🛠️ 실행 환경

- Python 3.x
- PyTorch 2.x
- torchvision
- Google Colab (T4 GPU)

---

## 📁 파일 구조
```
unet-pet-segmentation/
└── UNet_Practice_Oxford-IIIT Pet Dataset.ipynb
```
