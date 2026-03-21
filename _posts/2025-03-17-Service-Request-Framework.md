---
layout: post
title: "Service Request Framework"
date: 2026-03-17 10:00:00 +0800
categories: [servicenow itsm, korean]
tags: [request, workflow, flow, framework, korean]
---
<br>

ServiceNow의 ITSM 모듈의 기본인 Service Request의 뼈대가 되는 request flow를 공유하려고 합니다.
해당 flow를 구현할때는 별도의 application scope을 활용하여 관리하시는 것을 권장드립니다.

# 1. Intro
ServiceNow를 관리할때 catalog item의 flow를 item별 개별 flow를 만들어서 관리하는 경우가 많습니다.
허나 해당 방식은 다음과 같은 문제가 생길 수 있습니다.
- Catalog Item마다 개별 Flow를 만들다 보면 Flow 수가 계속 늘어남
- 작은 변경도 여러 Flow를 각각 수정해야 함
- 운영자가 전체 Service Request Flow를 한눈에 보기 어려움

<hr/>

# 2. Why I Built This

- Request fulfillment 패턴이 대부분 비슷했음
- 차이는 승인자, task 생성 대상, task 템플릿, 조건 정도였음
- 그래서 item마다 Flow를 따로 갖는 구조가 비효율적이었음
- 로직을 Flow 자체에 넣기보다 configuration-driven 구조로 바꾸고 싶었음

Instead of creating and maintaining separate flows for each catalog item, I wanted to move the variability into configuration and keep the execution logic standardized.

이 한 문장이 글의 중심이 됩니다.

<hr/>

# 3. The Core Idea

Flow-driven 운영 → Config-driven 운영

- 모든 flow는 하나의 workflow로만 동작.
- Item별 동작은 flow는 custom table의 record로 제어
- Custom table의 record에 따라 approval, catalog task 등의 동작을 order 순서에 따라 처리

<hr/>

# 4. Architecture Overview

예시 구조:
	•	Catalog Item
	•	Request / RITM
	•	Standard Request Flow
	•	Custom Configuration Table
	•	Approval Logic
	•	Catalog Task Creation Logic
	•	Optional Assignment / Conditions

간단히 글로 표현하면:
	1.	User submits a catalog item
	2.	Standard flow starts
	3.	The flow reads matching configuration from the custom table
	4.	Based on configuration:
	•	approval is triggered
	•	catalog tasks are generated
	•	assignment is determined
	•	optional conditions are evaluated
	5.	Request proceeds without item-specific flow duplication

<hr/>

# 5. Custom Table Design



예시 필드:
	•	Active
	•	Catalog Item
	•	Request Type / Category
	•	Order / Sequence
	•	Step Type
	•	Approval
	•	Catalog Task
	•	Notification
	•	Assignment Rule
	•	Approval Group / Approver
	•	Task Short Description Template
	•	Task Assignment Group
	•	Condition / Scripted Condition
	•	Task Template
	•	Auto Close / Wait for Completion 여부

중요한 건, 단순히 필드 나열보다 왜 필요한지 같이 적는 겁니다.

예:
	•	Order lets the flow process multiple steps in a predictable sequence.
	•	Step Type allows a single flow to handle approvals and fulfillment tasks using the same framework.
	•	Condition makes the model flexible enough to support item-specific exceptions without creating a new flow.

<hr/>

# 6. How the Flow Works

여기서는 실제 실행 흐름을 설명합니다.

예시:
	•	Trigger: Requested Item created
	•	Find related config records
	•	Sort by sequence
	•	Loop through each config row
	•	If step type is Approval → create approval / wait for result
	•	If step type is Catalog Task → create SCTASK
	•	If condition is not met → skip
	•	Continue until all rows are processed

이 부분은 screenshot 몇 장 넣으면 좋습니다.

추천 이미지:
	•	Flow overview
	•	Custom table list view
	•	Example config record
	•	Resulting approval / catalog task execution example

<hr/>

# 7. Example Scenario

예시 시나리오:
Example: New Software Request
	•	Step 1: Manager Approval
	•	Step 2: Security Review Task
	•	Step 3: Application Packaging Task
	•	Step 4: Fulfillment Task for Desktop Team

그리고 이 시나리오가 별도 item-specific flow 없이 config row만으로 처리된다고 보여주면 됩니다.

이런 식으로 적으면 좋습니다.

In the previous model, this request would likely require its own dedicated flow.
In the standardized model, the behavior is driven entirely by configuration records.

이 문장이 설득력이 큽니다.

<hr/>

# 8. Benefits

추천 5개:
	•	Reduced flow duplication
	•	Easier maintenance
	•	Faster onboarding for new catalog items
	•	Better visibility of request processing logic
	•	More scalable governance model

조금 더 실무적으로 바꾸면:
	•	운영자가 Flow Designer를 일일이 열지 않아도 설정 변경 가능
	•	신규 item 생성 시 config만 추가하면 됨
	•	approval / task 패턴 표준화 가능
	•	변경 영향도 파악이 쉬움
	•	배포 후 유지보수 비용 감소

<hr/>

# 9. Trade-offs / Limitations


	•	모든 케이스를 100% config로 일반화하기는 어려움
	•	너무 복잡한 예외 로직은 별도 처리 필요
	•	config table 설계가 부실하면 오히려 운영이 더 어려워질 수 있음
	•	권한 관리와 테스트 전략이 중요함
	•	디버깅 시 “설정 문제인지 로직 문제인지” 구분이 필요함

이런 문장이 좋습니다.

This approach does not eliminate complexity; it relocates complexity from multiple flows into a controlled configuration model.

아주 좋은 문장입니다.

<hr/>

# 10. When to Use / When Not to Use

Use this approach when:
	•	catalog items share similar fulfillment patterns
	•	approvals and tasks follow repeatable steps
	•	you want centralized maintenance

Avoid this approach when:
	•	each item has highly unique business logic
	•	every request requires deeply custom branching
	•	your team is not ready to govern a shared configuration model


<hr/>

# 11. Implementation Notes

여기에 이런 것들을 넣으면 좋습니다.
	•	custom table naming
	•	update set / app scope structure
	•	ACL considerations
	•	whether task templates are hard-coded or configurable
	•	how approval outcome is handled
	•	how failed or skipped steps are logged
	•	whether the flow supports parallel vs sequential steps
	•	reporting/dashboard implications

<hr/>

# 12. Conclusion

해당 구조는 Flow 개수를 줄이는 것이 목적이 아니라 Request fulfillment를 표준화 가능하고 운영 가능한 구조로 바꾸는 것이 목적이다.
비슷한 Catalog Item이 많은 조직에서는 특히 효과적이다.