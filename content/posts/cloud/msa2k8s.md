---
title: "MSA 애플리케이션을 쿠버네티스로 배포하기 - k8s"
author: "Sin SeHwan"
#authorAvatarPath: "/avatar.jpeg"
date: "2025-08-29"
summary: "MSA 애플리케이션을 쿠버네티스로 배포하기 - k8s"
description: "MSA to k8s Manifest - cloud"
toc: true
readTime: true
autonumber: true
math: true
tags: ["cloud"]
showTags: true
hideBackToTop: false
fediverse: "@username@instance.url"
---

# 필요한 항목과 이유

## 배포 환경
> MSA 애플리케이션 정의

- 서비스 설명
    - 서비스 이름 및 서비스를 구성하는 마이크로서비스들의 목록

- 의존 서비스
    - 장애 발생 시 의존성을 파악하기 위해 필요

- 볼륨 구성
    - 데이터 저장이 필요한 경우, 볼륨 종류, 크기, 접근 모드 명시

- 내부 서비스 포트(Service Port)
    - 파드는 동적으로 IP를 할당받기에 직접 접근하기가 어렵다. Cluster의 IP와 파드를 연결 주소를 고정

- 외부 노출 방식
    - 서비스의 공개 범위에 따라 외부 노출 방식 결정 필요
        - ClusterIP : 클러스터 내부에서만 접근
        - NodePort : 테스트나 간단한 외부 노출 - 테스트 시나리오에서는 해당 방식 사용 예정
        - LoadBalancer : 클라우드 환경에서 외부 로드밸런서 도입
        - Ingress 하나의 IP로 여러 도메인/경로 기반 라우팅 제공

> 마이크로서비스 기본 정보

- 컨테이너 이미지 및 버전 정보
    - 쿠버네티스가 어떤 이미지를 가져와서 실행해야 하는지 결정 필요.

- 마이크로서비스 포트
    - 마이크로서비스가 실제 사용하는 포트.

- 환경 변수
    - 서비스 동작에 필요한 설정 값.
        - 보안 수준에 따라 ConfigMap과 Secret으로 구분

- 리소스 설정
    - 컨테이너가 안정적으로 동작하기 위한 기본적인 리소스 요청 및 제한

- 복제 수(Replicas)
    - 기본적으로 실행할 Pod의 개수

## 조직 내부 정책

> 조직 내부 정책

- 레이블 규칙
    - 리소스를 팀, 프로젝트 등의 기준으로 식별하고 특정 그룹에만 정책을 반영하기 위한 요소
- 리소스 할당 기준
    - 서비스에 따라 할당 가능한 리소스의 최소/최대 규정
        - 동적 운영 정보 기반 개선의 기준 정보
- 보안 및 운영 정책
    - 보안과 안정성을 위한 규정 및 제한사항
        - e.g. latest 태그 사용 금지

# 필요한 항목이 배포 파일에서 반영돼야 하는 곳

## 배포 환경

> MSA 애플리케이션 정의

|항목|Kubernetes 리소스|설명|
| --------- | ------------------------ | ------------------------------------------------------------------------------------------------------- |
|서비스 설명|Deployment, Service|서비스 이름 및 서비스 구성 요소 (metadata.name, metadata.labels) |
|의존 서비스|ConfigMap|각각의 마이크로서비스가 의존하는 다른 서비스의 주소나 설정 정보|
|볼륨 구성|Deployment|컨테이너에 마운트 할 볼륨 정보 (volumeMounts)|
|내부 서비스 포트|Service|클러스터 내부에서 다른 서비스들이 접근할 수 있는 고유 포트 정보|
|외부 노출 방식|Service|Service의 type(NodePort, LoadBalancer)을 지정하거나 Ingress 리소스 사용|

> 마이크로서비스 기본 정보

|항목|Kubernetes 리소스|설명|
| --------- | ------------------------ | ------------------------------------------------------------------------------------------------------- |
| 컨테이너 이미지 경로 및 버전    | Deployment               | 실행할 컨테이너 이미지 경로와 버전 (spec.template.spec.containers.image)|
|마이크로서비스 포트|Deployment|컨테이너가 마이크로서비스와 연결할 포트 지정(spec.template.spec.containers.ports.containerPort)|
|환경 변수|ConfigMap, Secret|서비스 동작에 필요한 설정 값|
| 리소스 설정        | Deployment               | CPU/메모리 리소스 요청(requests), 한계(limits) 설정 (spec.template.spec.containers.resources)         |
| 복제 수 (Replicas)      | Deployment               | 파드 수 지정 (spec.replicas)      |

## 조직 내부 정책

> 조직 내부 정책

|항목|Kubernetes 리소스|설명|
| --------- | ------------------------ | ------------------------------------------------------------------------------------------------------- |
| 레이블 규칙  | 모든 리소스  | 조직 규칙에 맞게 label 추가 (metadata.labels)     |
| 리소스 할당 기준 | Deployment | 팀/프로젝트 단위로 할당 가능한 리소스의 총량이나 개별 컨테이너의 최소/최대 요구량 규정 |
| 보안 및 운영 정책   | Deployment, SecurityContext | 보안과 안정성을 위한 규정 및 제한사항(e.g. latest 태그 사용 금지) |