---
layout: single
title: "[컴퓨터비전] 카메라 캘리브레이션"
categories:	
    - cv
    - deep learning
tags:
    - ['딥러닝', '비전', '전처리']

toc: true
toc_sticky : true

author_profile: false
sidebar:
    nav: "docs"

last_modified_at:
use_math: true
---

# 카메라 캘리브레이션(Camera calibration)이란?

----

- **카메라의 파라미터(parameters)를 추정**하는 과정을 말함.
- 실세계의 3D 점과 이미지의 2D 투영(픽셀)간의 정확한 관계를 결정하는데 필요한, 카메라에 대한 모든 정보(파라미터 또는 계수)를 의미함.
- 파라미터를 복구하는 일반적인 방법
  - 카메라/렌즈 시스템의 **내부 파라미터(Internal parameters)**: 초점 거리(focal length), 광학 중심(optical center), 렌즈의 방사 왜곡 계수(radial distortion coefficients of the lens).
  - **외부 파라미터(External parameters)**: 카메라의 방향(회전 및 이동, Rotation and translation)을 나타냄.
- 기하학적 캘리브레이션을 사용하여 추정된 렌즈의 파라미터로 이미지 왜곡을 제거

![calibration_result1]({{site.url}}/../images/2023-10-08-camera_calibration/calibration_result1.png)













# 참고자료

---

- 
