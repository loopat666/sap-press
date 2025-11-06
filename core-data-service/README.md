# README

Core Data Service 챕터별 요약

## Chapter 1 

shows you how to define your first simple CDS views quickly. In addition to the essential parts of a CDS view, we’ll also briefly introduce the most important development tools. 

> [!note]
> 1장에서는 첫 번째 간단한 CDS 뷰를 빠르게 정의하는 방법을 보여줍니다. CDS 뷰의 필수 요소뿐만 아니라 가장 중요한 개발 도구들도 간단히 소개합니다.﻿

## Chapter 2 

provides the fundamentals and technical basics of CDS data models. For this purpose, we present the relevant CDS artifacts from the developer’s point of view, including their components, using modeling examples. 

> [!note]
> 2장에서는 CDS 데이터 모델의 기본 원리와 기술적 기초를 제공합니다. 이를 위해 개발자의 관점에서 관련된 CDS 아티팩트와 그 구성요소를 모델링 예제를 통해 소개합니다.


### Overview of CDS View Syntax

```cds
/*comment*/
//other comment
//annotations
@AccessControl.authorizationCheck: #MANDATORY
@EndUserText.label: 'View Definition'
//view definition
define view entity Z_ViewDefinition
//parameter definition
 with parameters
 P_SalesOrderType : auart
//data source of selection with alias name
 as select from ZI_SalesOrderItem as ITEM
//join
 left outer to exact one join ZI_SalesOrder as SO
 on SO.SalesOrder = ITEM.SalesOrder
//association definition
 association [0..1] to ZI_Product as _Product on
 $projection.RenamedProduct = _Product.Product
{
 //projected field as key
 key ITEM.SalesOrder,
 //projected field used in association definition
 key ITEM.Product as RenamedProduct,
 //constant
 abap.char'A' as Constant,
 //calculated field
 concat( ITEM.SalesOrder, ITEM.Product ) as CalculatedField,
 //aggregate
 count(*) as NumberOfAggregatedItems,
 //projected association
 ITEM._SalesOrder,
 //association exposure
 _Product
}
//filter conditions based on join partner and parameter
where
 SO.SalesOrderType = $parameters.P_SalesOrderType
//aggregation level
group by
 ITEM.SalesOrder,
 ITEM.Product 
```

### Key Fields

키필드의 정의.

```cds
define view entity ZI_SalesOrderItem
 as select from zsalesorderitem
{
 key salesorder as SalesOrder,
 key salesorderitem as SalesOrderItem,
 product as Product
} 

```

### Cast Operations

타입변환

```cds
define view entity ZZABAP02CDS2511_0003
  as select from t000
{
  key mandt      as client,
      mtext      as Mtext,
      ort01      as Ort01,
      mwaer      as Mwaer,
      adrnr      as Adrnr,
      cccategory as Cccategory,
      cccoractiv as Cccoractiv,
      ccnocliind as Ccnocliind,
      cccopylock as Cccopylock,
      ccnocascad as Ccnocascad,
      ccsoftlock as Ccsoftlock,
      ccorigcont as Ccorigcont,
      ccimaildis as Ccimaildis,
      cctemplock as Cctemplock,
      changeuser as Changeuser,
      changedate as Changedate,
      logsys     as Logsys,
      logsys     as ProjectedField,
      
      // ---- Cast Operations ---
      '20251101' as CharDate,
      cast ( '20251101' as abap.dats ) as DateField,
      cast( cast( 'E' as abap.lang ) as sylangu preserving type ) as LanguageField,
      1.2 as FloatPointField,
      fltp_to_dec( 1.2 as abap.dec( 4, 2 ) ) as DecimalField
      
      
}
```

### Type Literals

```cds
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Typed Literals'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZZABAP02CDS2511_0004
  as select from t000
{
  '  Char10  '                                            as CharacterValue,
  cast( '  Char10  ' as abap.char(10) )                   as CastCharacterValue,
  abap.char'  Char10  '                                   as TypedCharacterValue,
  cast( abap.char'  Char10  ' as char10 preserving type ) as CastTypedCharacterValue,

  1234.56                                                 as FloatingPointValue,
  abap.fltp'1234.56'                                      as TypedFloatingPointValue,

  fltp_to_dec(1234.56 as abap.dec(6,2))                   as ConvertedDecimalValue,
  abap.dec'1234.56'                                       as TypedDecimalValue,
  abap.dec'001234.5600'                                   as TypedDecimalValue2
}

```

### Simple Types

```cds
define view entity Z_ViewWithSimpleTypes
 as select distinct from t000
{
 cast ( abap.lang'E' as ZBT_LanguageA preserving type ) as Language1,
 cast ( abap.lang'E' as ZBT_LanguageD preserving type ) as Language2
} 

```

### Case Statements

```cds
define view entity ZZABAP02CDS2511_0005
  as select from spfli
{
  key carrid                             as Carrid,
  key connid                             as Connid,
      countryfr                          as Countryfr,
      cityfrom                           as Cityfrom,
      airpfrom                           as Airpfrom,
      countryto                          as Countryto,
      cityto                             as Cityto,
      airpto                             as Airpto,

      case (carrid)
          when 'AA' then 'A1'
          when 'Az' then 'A2'
          else '' end                    as isStandardOrder,

      cast( case (carrid)
         when 'AA' then 'A1-1'
         when 'Az' then 'A2-2'
         else '' end as abap.char( 4 ) ) as isStandardOrderChar4,

      case when carrid = 'AA' then 'A1'
           when carrid = 'AZ' then 'A2'
           else '' end                   as isStandardOrder2

}

```



## Chapter 3

we explain the basics, definition, and usage of associations in CDS models via ABAP implementation and special association types, such as compositions. 

> [!note]
> 3장에서는 ABAP 구현을 통한 CDS 모델에서의 어소시에이션의 기본 개념, 정의, 사용법과 함께 컴포지션과 같은 특수 어소시에이션 유형도 설명합니다.﻿

## Chapter 4

we explain the basics of CDS annotations and their propagation logic using examples. CDS annotations enrich the data models with semantic information. This information is interpreted by CDS consumers and controls, and then influence, for example, the presentation on the UI or the aggregation behavior of an analytical application. 

> [!note]
> 4장에서는 CDS 어노테이션의 기본 개념과 그 전파 로직을 예제를 통해 설명합니다. CDS 어노테이션은 데이터 모델을 의미론적 정보로 풍부하게 만듭니다. 이 정보는 CDS 소비자와 제어 로직에 의해 해석되어, 예를 들어 사용자 인터페이스(UI)에서의 표시 방식이나 분석 애플리케이션의 집계 동작에 영향을 줍니다.﻿

## Chapter 5

we deal with the authorizations for accessing the data exposed by the CDS models. Access controls using the CDS data control language (DCL) enable you to restrict the selection results of CDS views according to the user’s authorizations via classic ABAP authorization objects. We explain the basics of CDS access controls and demonstrate their concrete use. 

> [!note]
> 5장에서는 CDS 모델을 통해 노출된 데이터에 대한 접근 권한에 대해 다룹니다. CDS 데이터 제어 언어(DCL)를 활용한 접근 제어는 고전적인 ABAP 권한 객체를 통해 CDS 뷰의 조회 결과를 사용자의 권한에 따라 제한할 수 있게 해줍니다. CDS 접근 제어의 기본 개념을 설명하고, 그 구체적인 활용 방법을 시연합니다.

## Chapter 6

you get an insight into the modeling and definition of business services based on a special view type, projection views, service definitions, and service bindings focusing on OData protocol–based bindings. Business services allow you to expose your CDS models to external consumers.

> [!note]
> 비즈니스 서비스를 모델링하고 정의하는 방법에 대해, 특별한 뷰 타입인 프로젝션 뷰, 서비스 정의, 그리고 OData 프로토콜 기반 바인딩에 초점을 맞춘 서비스 바인딩을 통해 살펴볼 수 있습니다. 비즈니스 서비스는 CDS 모델을 외부 소비자에게 노출할 수 있도록 해줍니다.﻿

## Chapter 7 

we examine CDS table functions, which enable you to integrate native SAP HANA functions into the CDS view stack using SQLScript. We illustrate the modeling of CDS table functions and highlight their special features. 

> [!note]
> CDS 테이블 함수에 대해 살펴보며, SQLScript를 활용해 네이티브 SAP HANA 함수를 CDS 뷰 스택에 통합할 수 있게 해줍니다. CDS 테이블 함수의 모델링 방법을 설명하고, 그 고유한 특징도 강조합니다.﻿

## Chapter 8 

provides an overview of the application architecture in SAP S/4HANA and the positioning of CDS within this architecture. Using examples, we show how important aspects of application data are modeled with CDS, such as field labels, field semantics, foreign key relationships, language-dependent texts, entity compositions, and time-dependent data. 

> [!note]
>SAP S/4HANA의 애플리케이션 아키텍처와 그 아키텍처 내에서 CDS가 어떤 위치를 점하고 있는지에 대한 개요를 제공합니다. 예시를 통해 CDS를 활용하여 애플리케이션 데이터의 중요한 측면—필드 라벨, 필드 의미, 외래키 관계, 언어 종속 텍스트, 엔터티 컴포지션, 시간 종속 데이터—등을 어떻게 모델링하는지 보여줍니다.﻿

## Chapter 9

we explain the virtual data model (VDM) of SAP S/4HANA, which is formed by selected CDS views that meet centrally defined quality and modeling aspects. The modeling rules used are presented schematically via examples. The explanations are intended to help you understand the CDS models delivered by SAP and enable you to use them purposefully in your own developments. 

> [!note]
> SAP S/4HANA의 가상 데이터 모델(VDM)에 대해 설명합니다. VDM은 중앙에서 정의된 품질과 모델링 측면을 충족하는 특정 CDS 뷰들로 구성됩니다. 사용된 모델링 규칙은 도식적인 예시를 통해 제시합니다. 이러한 설명들은 SAP에서 제공하는 CDS 모델을 이해하고, 이를 자체 개발에 목적에 맞게 활용할 수 있도록 돕기 위한 것입니다.﻿

## Chapter 10

we focus on analytical applications. We briefly discuss the SAP S/4HANA architecture for embedded analytics and explain how CDS views can be used for this. The analytical model used is based on a consistent network of interlinked analytical CDS views and exposed via analytical queries. We use examples to show you the relationships between these CDS views and their special features and analytical calculations. 

> [!note]
>분석 애플리케이션에 초점을 맞춥니다. 임베디드 애널리틱스를 위한 SAP S/4HANA 아키텍처를 간단히 논의하고, CDS 뷰를 어떻게 활용할 수 있는지 설명합니다. 사용된 분석 모델은 상호 연결된 분석용 CDS 뷰의 일관된 네트워크를 기반으로 하며, 분석 쿼리를 통해 공개됩니다. 예시를 활용해 이러한 CDS 뷰들 간의 관계, 고유한 특성, 분석 계산 방식 등을 보여줍니다.﻿

## Chapter 11

we then deal with transactional applications. In addition to modeling and executing read accesses, CDS also is the basic data model for enabling transactional processing. The integration of a transactional runtime based on the ABAP RESTful application programming model and the special aspects of transactional applications, such as locking, authorizations, feature control, business logic (e.g., actions, determinations, and validations), and business events are explained using appropriate examples. In addition, the draft concept is introduced and explained in detail.

> [!note]
> 이어서 트랜잭션 애플리케이션에 대해 다룹니다. CDS는 조회 액세스의 모델링 및 실행뿐만 아니라, 트랜잭션 처리를 가능하게 하는 기본 데이터 모델 역할도 합니다. ABAP RESTful 애플리케이션 프로그래밍 모델 기반의 트랜잭션 런타임 통합, 그리고 잠금, 권한, 기능 제어, 비즈니스 로직(예: 액션, 결정, 검증), 비즈니스 이벤트와 같은 트랜잭션 애플리케이션의 특별한 측면들을 적절한 예시를 통해 설명합니다. 또한 드래프트 개념(draft concept)도 도입되어 자세히 설명됩니다.﻿

## Chapter 12

we introduce two options for the flexible modeling in CDS of hierarchical structured data with concrete examples. The hierarchical models are interpreted directly by the underlying infrastructure (e.g., in analytical applications) or can be used as powerful functions for navigation or aggregation of these hierarchies in your ABAP implementations. 

> [!note]
> CDS에서 계층적으로 구조화된 데이터를 유연하게 모델링할 수 있는 두 가지 옵션을 구체적인 예시와 함께 소개합니다. 이러한 계층 모델은 기반 인프라(예: 분석 애플리케이션)에서 직접 해석되거나, ABAP 구현에서 계층의 탐색이나 집계 기능을 위한 강력한 기능으로 사용할 수 있습니다.﻿

## Chapter 13 

shows you that CDS can also be used as a basis for modeling value helps and free-text searches. You’ll learn how the value helps and search functions can be integrated and used in certain use cases, for example, for OData exposure. 

> [!note]
> CDS가 값 도우미(value help) 및 자유 텍스트 검색을 모델링하는 기반으로도 활용될 수 있음을 보여줍니다. 값 도우미와 검색 기능이 특정 사용 사례, 예를 들어 OData로 데이터를 노출할 때 어떻게 통합되고 활용될 수 있는지를 배우게 됩니다.

## Chapter 14 

addresses the question of lifecycle and stability of CDS and ABAP RESTful application programming model artifacts as well as if and how these can be changed within an upgrade or hot fix. Stability contracts provide security for developers and consumers of these artifacts. A defined lifecycle controls the release of these artifacts as well as their deprecation and replacing successors.

> [!note]
> CDS와 ABAP RESTful 애플리케이션 프로그래밍 모델 산출물(artifact)의 라이프사이클과 안정성, 그리고 업그레이드 또는 핫픽스에서 이러한 산출물을 변경할 수 있는지, 그리고 변경 방법에 대한 질문을 다룹니다. 안정성 계약은 개발자와 소비자 모두에게 보장을 제공합니다. 정의된 라이프사이클은 이러한 산출물의 릴리스, 사용 중지, 그리고 대체 산출물로의 전환을 관리합니다.﻿

## Chapter 15 

provides an overview of the possibilities for modification-free enhancements by customers and partners. In SAP S/4HANA Cloud, public edition, special tailored apps support an experienced user with special authorizations— the key user—in performing necessary enhancements. These enhancements must be kept stable from a lifecycle perspective to ensure smooth software. This requires special preparations during application development and adherence to stability contracts. Further developer extensibility is explained by using CDS view extensions. 

> [!note]
> 고객과 파트너가 수정 없이 확장할 수 있는 다양한 가능성에 대해 개요를 제공합니다. SAP S/4HANA Cloud, public edition에서는 특별한 권한을 지닌 숙련된 사용자인 키 유저(key user)가 필요한 확장을 수행할 수 있도록 맞춤형 앱이 지원됩니다. 이러한 확장은 소프트웨어가 원활히 동작하기 위해 라이프사이클 관점에서 안정적으로 유지되어야 하며, 이를 위해 애플리케이션 개발 시 특별한 준비와 안정성 계약 준수가 필요합니다. 추가적인 개발자 확장은 CDS 뷰 확장 기능을 활용해 설명합니다.﻿

## Chapter 16 

introduces the creation of automated tests for your CDS models as well as the transactional runtime and its implementations. The test double framework presented here allows you to exchange the data sources of CDS views as well as certain parts of the behavior implementation and thus to decouple the test execution from the actual persistent data or productive application logic. We explain test automation with the test double frameworks using examples.

> [!note]
> CDS 모델과 트랜잭션 런타임 및 그 구현에 대한 자동화 테스트 생성 방법을 소개합니다. 여기서 제시하는 테스트 더블 프레임워크는 CDS 뷰의 데이터 소스와 행동 구현의 특정 부분을 교체할 수 있게 하여, 테스트 실행을 실제 영구 데이터나 운영 애플리케이션 로직과 분리할 수 있도록 해줍니다. 테스트 더블 프레임워크를 활용한 테스트 자동화 방법을 예제를 통해 설명합니다.﻿

## Chapter 17

we recommend practical ways for troubleshooting to find and correct errors and problems in the context of CDS and the ABAP RESTful application programming model. We describe the tools relevant for troubleshooting and explain how to use them. In addition, we illustrate some pitfalls that you may come across when developing your CDS models.

> [!note]
> CDS 및 ABAP RESTful 애플리케이션 프로그래밍 모델의 맥락에서 오류와 문제를 찾고 수정하기 위한 실용적인 문제 해결 방법을 제안합니다. 문제 해결에 필요한 도구들을 설명하고, 그 사용법도 안내합니다. 또한, CDS 모델을 개발할 때 마주칠 수 있는 몇 가지 함정도 함께 설명합니다.﻿


An overview of the CDS annotations presented in this book
is included in 

### Appendix A 

as a quick reference. For an overview of all available CDS annotations, refer to the SAP
documentation.

### Appendix B

you can find an overview of how to migrate an application based on the ABAP programming model for SAP Fiori (that was the basic for transactional applications in
the first edition of this book) to the ABAP RESTful application programming model.

