---
lab:
  title: '랩: Azure Stack Hub에서 공용 IP 주소 관리'
  module: 'Module 5: Manage Infrastructure'
ms.openlocfilehash: fd9c6f35024c4753fe27d86cef8fd47dd48764f3
ms.sourcegitcommit: fd0b6231a00e8c86b46d914b2b6c4d984bc19902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2022
ms.locfileid: "139256869"
---
# <a name="lab---manage-public-ip-addresses-in-azure-stack-hub"></a>랩 - Azure Stack Hub에서 공용 IP 주소 관리
# <a name="student-lab-manual"></a>학생용 랩 매뉴얼

## <a name="lab-dependencies"></a>랩 종속성

- 없음

## <a name="estimated-time"></a>예상 소요 시간

30분

## <a name="lab-scenario"></a>랩 시나리오

여러분은 Azure Stack Hub 환경의 운영자입니다. 공용 IP 주소 리소스를 관리해야 합니다. 

## <a name="objectives"></a>목표

이 랩을 완료하면 다음을 수행할 수 있습니다.

- 공용 IP 주소 리소스 관리

## <a name="lab-environment"></a>랩 환경

이 랩에서는 AD FS(Active Directory Federation Services)와 통합된 ASDK 인스턴스(ID 공급자로 백업된 Active Directory)를 사용합니다. 

랩 환경에는 다음과 같은 구성 요소가 포함됩니다.

- 다음 액세스 지점을 사용하여 **AzS-HOST1** 서버에서 실행되는 ASDK 배포:

  - 관리자 포털: https://adminportal.local.azurestack.external
  - 관리자 ARM 엔드포인트: https://adminmanagement.local.azurestack.external
  - 사용자 포털: https://portal.local.azurestack.external
  - 사용자 ARM 엔드포인트: https://management.local.azurestack.external

- 관리자:

  - ASDK 클라우드 운영자 사용자 이름: **CloudAdmin@azurestack.local**
  - ASDK 클라우드 운영자 암호: **Pa55w.rd1234**
  - ASDK 호스트 관리자 사용자 이름: **AzureStackAdmin@azurestack.local**
  - ASDK 호스트 관리자 암호: **Pa55w.rd1234**

이 랩을 진행하면서 PowerShell을 통해 Azure Stack Hub를 관리하는 데 필요한 소프트웨어를 설치합니다. 그리고 사용자 계정을 추가로 만듭니다.

## <a name="instructions"></a>Instructions

### <a name="exercise-1-create-an-offer-as-a-cloud-operator"></a>연습 1: 제안 만들기(클라우드 운영자 역할)

이 연습에서는 클라우드 운영자 역할을 맡아 먼저 공용 IP 주소 사용량을 검토한 다음 네트워크 서비스가 포함된 요금제, 그리고 이 요금제가 포함된 제안을 만듭니다. 그런 다음 사용자가 해당 제안을 기준으로 구독을 만들 수 있도록 제안을 공개합니다. 연습은 다음 작업으로 구성됩니다.

1. 공용 IP 주소 사용량 검토(클라우드 운영자 역할)
1. 네트워크 서비스가 포함된 요금제 만들기(클라우드 운영자 역할)
1. 요금제를 기준으로 제안을 만들고 공용으로 설정(클라우드 운영자 역할)


#### <a name="task-1-review-public-ip-address-usage-as-a-cloud-operator"></a>작업 1: 공용 IP 주소 사용량 검토(클라우드 운영자 역할)

이 작업에서는 다음을 수행합니다.

- 공용 IP 주소 사용량 검토(클라우드 운영자 역할)

1. **AzS-HOST1** 에 연결된 원격 데스크톱 세션에서 [Azure Stack Hub 관리자 포털](https://adminportal.local.azurestack.external/)이 표시된 웹 브라우저 창을 열고 CloudAdmin@azurestack.local로 로그인합니다.
1. Azure Stack Hub 사용자 포털의 **대시보드** 페이지 **리소스 공급자** 타일에서 **네트워크** 를 클릭합니다. 
1. **네트워크** 블레이드에서 **공용 IP 풀 사용량** 그래프, 그리고 사용 중인 IP 주소 및 사용 가능한 IP 주소 수를 확인합니다.

#### <a name="task-2-create-a-plan-consisting-of-the-network-services-as-a-cloud-operator"></a>작업 2: 네트워크 서비스가 포함된 요금제 만들기(클라우드 운영자 역할)

이 작업에서는 다음을 수행합니다.

- 네트워크 서비스가 포함된 요금제 만들기(클라우드 운영자 역할)

1. Azure Stack Hub 관리자 포털이 표시된 웹 브라우저 창에서 **+ 리소스 만들기** 를 클릭합니다. 
1. **새로 만들기** 블레이드에서 **제안+요금제** 를 클릭합니다.
1. **제안+요금제** 블레이드에서 **요금제** 를 클릭합니다.
1. **새 요금제** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다.

    - 표시 이름: **Network-plan1**
    - 리소스 이름: **network-plan1**
    - 리소스 그룹: 새 리소스 그룹 **network-plans-RG** 의 이름.

1. **다음: 서비스 >** 를 클릭합니다.
1. **새 요금제** 블레이드의 **서비스** 탭에서 **Microsoft.Network** 체크박스를 선택합니다.
1. **다음: 할당량>** 을 클릭합니다.
1. **새 요금제** 블레이드의 **할당량** 탭에서 **새로 만들기** 를 선택합니다.
1. **네트워크 할당량 만들기** 블레이드에서 다음 설정을 지정하고 **확인** 을 클릭합니다.

    - 이름: **Network-plan1-quota**
    - 가상 네트워크. **2**
    - 가상 네트워크 게이트웨이: **2**
    - 네트워크 연결: **2**
    - 공용 IP: **20**
    - NIC: **20**
    - 부하 분산 장치: **5**
    - 네트워크 보안 그룹: **20**

1. **검토 + 만들기** 를 클릭한 다음, **만들기** 를 클릭합니다.

    >**참고**: 배포가 완료될 때까지 기다리세요. 몇 초면 끝납니다.


#### <a name="task-3-create-an-offer-based-on-the-plan-as-a-cloud-operator"></a>작업 3: 요금제를 기준으로 제안 만들기(클라우드 운영자 역할)

이 작업에서는 다음을 수행합니다.

- 요금제를 기준으로 제안 만들기(클라우드 운영자 역할)

1. Azure Stack Hub 관리자 포털에서 **+ 리소스 만들기** 를 클릭합니다. 
1. **새로 만들기** 블레이드에서 **제안+요금제** 를 클릭합니다.
1. **제안+요금제** 블레이드에서 **제안** 을 클릭합니다.
1. **새 제안 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다.

    - 표시 이름: **Network-offer1**
    - 리소스 이름: **network-offer1**
    - 리소스 그룹: 새 리소스 그룹 **network-offers-RG**
    - 이 제안을 공개로 설정: **예**

1. **다음: 기본 플랜 >** 을 클릭합니다. 
1. **새 제안 만들기** 블레이드의 **기본 요금제** 탭에서 **Network-plan1** 항목 옆의 체크박스를 선택합니다.
1. **다음: 추가 기능 플랜 >** 을 클릭합니다.
1. **추가 요금제** 설정은 기본값으로 유지하고 **검토 + 만들기** 를 클릭한 다음 **만들기** 를 클릭합니다.

    >**참고**: 배포가 완료될 때까지 기다리세요. 몇 초면 끝납니다.

>**복습**: 이 연습에서는 요금제, 그리고 해당 요금제를 기반으로 하는 공용 제안을 만들었습니다.


### <a name="exercise-2-create-public-ip-address-resources-as-a-user"></a>연습 2: 공용 IP 주소 리소스 만들기(사용자 역할)

이 연습에서는 사용자 역할을 맡아 첫 번째 연습에서 만든 제안에 등록하고 새 구독을 만든 다음 해당 구독에서 공용 IP 주소 리소스를 만듭니다. 연습은 다음 작업으로 구성됩니다.

1. 제안에 등록(사용자 역할)
1. IP 주소 리소스 만들기(사용자)

#### <a name="task-1-sign-up-for-the-offer-as-a-user"></a>작업 1: 제안에 등록(사용자 역할)

이 작업에서는 다음을 수행합니다.

- 제안에 등록(사용자 역할)

1. 1. **AzS-HOST1** 에 연결된 원격 데스크톱 세션 내에서 웹 브라우저의 InPrivate 세션을 시작합니다.
1. 웹 브라우저 창에서 [Azure Stack Hub 사용자 포털](https://portal.local.azurestack.external)로 이동하여 **t1u1@azurestack.local** 및 암호 **Pa55w.rd** 로 로그인합니다.
1. Azure Stack Hub 사용자 포털의 대시보드에서 **구독 가져오기** 를 클릭합니다.
1. **구독 가져오기** 블레이드의 **이름** 텍스트 상자에 **T1U1-network-subscription1** 을 입력합니다.
1. 제안 목록에서 **Network-offer1** 을 선택하고 **만들기** 를 클릭합니다.
1. **구독이 생성되었습니다. 구독 사용을 시작하려면 포털을 새로 고쳐야 합니다.** 메시지가 표시되면 **새로 고침** 을 클릭합니다.

#### <a name="task-2-create-a-public-ip-address-as-a-user"></a>작업 2: 공용 IP 주소 만들기(사용자)

이 작업에서는 다음을 수행합니다.

- Azure Stack Hub 사용자 포털을 사용하여 공용 IP 주소 만들기(사용자)

1. Azure Stack Hub 사용자 포털의 대시보드에서 **+ 리소스 만들기** 를 클릭합니다.
1. **Marketplace** 블레이드에서 **공용 IP 주소** 를 선택하고 **공용 IP 주소** 블레이드에서 **만들기** 를 선택합니다.
1. **공용 IP 주소 만들기** 블레이드에서 다음 설정을 지정하고 **만들기** 를 선택합니다(다른 모든 항목은 기본값으로 유지).

    - 이름: **t1-pip1**
    - IP 주소 할당: **동적**
    - 유휴 시간 제한(분): **4**
    - DNS 이름 레이블: **t1-pip1**
    - 구독: **T1U1-network-subscription1**
    - 리소스 그룹: 이름이 **publicIPs-RG** 인 새 리소스 그룹
    - 위치: **로컬**

1. IP 주소 리소스가 프로비저닝될 때까지 기다립니다.

>**복습**: 이 연습에서는 사용자의 구독에서 공용 IP 주소 리소스를 만들었습니다.

### <a name="exercise-3-manage-public-ip-address-usage-as-a-cloud-operator"></a>연습 3: 공용 IP 주소 사용량 관리(클라우드 운영자 역할)

이 연습에서는 클라우드 운영자 역할을 맡아 공용 IP 주소 사용량을 검토 및 관리합니다. 연습은 다음 작업으로 구성됩니다.

1. 공용 IP 주소 사용량 검토
2. 공용 IP 주소 풀 추가

#### <a name="task-1-review-public-ip-address-usage-as-a-cloud-operator"></a>작업 1: 공용 IP 주소 사용량 검토(클라우드 운영자 역할)

이 작업에서는 다음을 수행합니다.

- 공용 IP 주소 사용량 검토(클라우드 운영자 역할)

1. Azure Stack Hub 관리자 포털이 표시되어 있으며 CloudAdmin@azurestack.local로 로그인되어 있는 웹 브라우저 창으로 전환합니다.
1. Azure Stack Hub 관리자 포털의 허브 메뉴에서 **대시보드** 를 클릭하고 **리소스 공급자** 타일에서 **네트워크** 를 클릭합니다.
1. **네트워크** 블레이드에서 **공용 IP 풀 사용량** 그래프, 그리고 사용 중인 IP 주소 및 사용 가능한 IP 주소 수를 다시 검토합니다.

    >**참고**: (사용자 역할로) 사용자 구독에서 추가로 만든 공용 IP 주소가 반영되어 개수가 변경되었을 것입니다.

#### <a name="task-2-add-a-public-ip-address-pool-as-a-cloud-operator"></a>작업 2: 공용 IP 주소 풀 추가(클라우드 운영자 역할)

이 작업에서는 다음을 수행합니다.

- 공용 IP 주소 풀 추가

1. Azure Stack Hub 관리자 포털이 표시된 웹 브라우저 창의 **네트워크** 블레이드에서 **공용 IP 풀 사용량** 타일을 클릭합니다.

    >**참고**: **공용 IP 풀** 블레이드에  **‘시작’ 단독 작업이 진행 중입니다. 해당 작업이 진행 중인 동안에는 노드 추가 및 IP 풀 추가 작업이 사용하지 않도록 설정됩니다. 활동 로그를 확인하려면 여기를 클릭하세요.** 메시지가 표시되면 ‘시작’ 작업이 완료될 때까지 기다렸다가 다음 단계를 진행해야 합니다.

1. **공용 IP 풀** 블레이드에서 **+ IP 풀 추가** 를 클릭합니다. 
1. **IP 풀 추가** 블레이드에서 다음 설정을 지정하고 **추가** 를 클릭합니다.

    - 이름: **공용 풀 1**
    - 지역: **로컬**
    - 주소 범위(CIDR 블록): **192.168.110.0/24**

1. 변경 내용이 적용될 때까지 기다렸다가 **네트워크** 블레이드로 다시 이동합니다.
1. **공용 IP 풀 사용량** 그래프를 검토하여 사용 가능한 IP 주소 수가 변경되었음을 확인합니다.

>**복습**: 이 연습에서는 공용 IP 주소 풀을 검토 및 구성했습니다.
