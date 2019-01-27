# 分散标识符(DID) v0.11
> 分散标识符（DID）的数据模型和语法

# 摘要

分散标识符（DID）是一种新的标识符，他用于可验证的“自主（self-sovereign）”数字身份。DIDs完全处于已完成的主体的控制之下，独立于任何集中式注册中心、身份提供者或证书颁发机构。DID是将一个已完成的主题与该主题的可靠交互联系起来的url。DID决心做文档——简单的文档描述如何使用特定的文档。每个文档都包含至少三样东西：加密材料、身份验证套件和服务端点。与身份验证套件相结合的加密材料提供了一组机制来进行身份验证（例如，公钥、匿名生物特征协议等）。服务端点支持与DID主题的可信交互。

该文档指定了所有DID支持的公共数据模型、格式和操作。

推荐阅读：[自主身份 | Linux中国](https://linux.cn/article-7652-1.html)

# 1 介绍

## 1.1 概况

传统的[身份管理（identity management）](https://en.wikipedia.org/wiki/Identity_management)系统是基于集中的权威，例如[公司目录服务（directory services）](https://en.wikipedia.org/wiki/Directory_service)、[证书颁发机构（certificate authorities）](https://en.wikipedia.org/wiki/Certificate_authority)或[域名注册中心（domain name registries）](https://en.wikipedia.org/wiki/Domain_name_registry)。从密码信任验证的角度来看，每一个中央集权的权威都是其自身的[信任根源](https://en.wikipedia.org/wiki/Trust_anchor)。要使跨这些系统的身份管理工作需要实现[联合身份管理](https://en.wikipedia.org/wiki/Federated_identity)。

分布式记账技术（DLT）的出现，有时被称为区块链技术，提供了完全去中心化的身份管理的机会。在一个分散的身份系统中，实体可以自由地使用任何共享的信任根。全球分布式的分类账（或提供类似功能的分散式P2P网络）提供了一种管理信任根的方法，既没有中央集权，也没有单点故障。同时，DLTs和分散式身份识别系统使任何实体能够在任意数量的分布式独立的信任基础上创建和管理自己的标识符。

这些实体由分散的标识符（DID）标识。他们可以通过证明进行认证（例如，数字签名、保护隐私的生物识别协议等）。DID指的是文档。一个DID文档包含一组用于与实体交互的服务端点。按照[隐私设计](https://en.wikipedia.org/wiki/Privacy_by_design)原则，每个实体可能有必要的数量，以尊重实体想要的身份、角色和上下文的分离。

要使用特定的分布式账本或网络，需要在单独的方法规范中定义一个DID方法。一个DID方法指定了一个规则的集合，用于在特定的分类帐或网络上注册、解析、更新和撤销。

这种设计消除了对标识符集中注册的依赖，以及用于密钥管理的集中证书权威——层次化[PKI（公钥基础设施）](https://en.wikipedia.org/wiki/Public_key_infrastructure)中的标准模式。由于DID驻留在一个分布式账本上，每个实体都可以作为它自己的根权威一个被称为[DPKI（分布式公钥基础设施）](https://github.com/WebOfTrustInfo/rebooting-the-web-of-trust/blob/master/final-documents/dpki.pdf)的体系结构。

注意，也可以为注册在联邦或集中的身份管理系统中的标识符开发方法。对于他们来说，所有类型的标识符系统都可能增加对DIDs的支持。这在集中式、联邦和分散的标识符之间创建了一个互操作性的桥梁。

### 1.1.1 URI、URL和URN

DID是以URL为基础，所以了解什么是URL就非常重要，W3C在2001年9月[澄清](https://www.w3.org/TR/uri-clarification/)了URI(Uniform Resource Identifier 统一资源标识符)、URL(Uniform Resource Locator 统一资源定位器)和URN(Uniform Resource Name 统一资源名称)等术语。这三类标识符的主要区别是:

1. **URI**是识别抽象或物理资源的任何类型标识符（例如，URN、URL等）的术语。它可能是资源，也可能不是资源。
2. **URL**是任何类型的URI的术语，可以解析或取消引用，以定位Web上资源的表示（例如，Web页面、文件、图像等）。
3. **URN**是用于持久标识资源的特定类型URI的术语，也就是说，无论资源如何移动、更改标签、更改控制器等，都不会改变标识符。URN会永远保存

推荐阅读：[分清 URI、URL 和 URN | IBM](https://www.ibm.com/developerworks/cn/xml/x-urlni.html)

### 1.1.2 DID缘起

对分散标识符的需求不断增长，这对一种新类型的URL产生了两个具体的要求，这些新类型的URL仍然适合Web架构，并且有一些额外的要求，而这些要求是更传统的URL（例如基于HTTP的URL）所不具备的:

1. 今天绝大多数的URL都基于DNS名称或IP地址，这些DNS名称或IP地址最终依赖于集中管理的注册和控制。而新类型的URL不应该要求集中的权限来注册、解析、更新或撤销标识符。可以在没有任何此类权限的情况下创建和管理DID。

2.  可以通过加密方式验证其控件和相关元数据(包括公钥) 的URL。通过DIDs和DID文档进行身份验证与分布式账簿使用相同的公钥（public key）/私钥（private key）加密。

### 1.1.3 人性化标识符的作用

在不需要中央注册机构的情况下，DID实现了全局的唯一性。然而，这是以人类记忆能力为代价的。能够生成全局唯一标识符的算法会自动生成没有人类含义的随机字符串。这就证明了[帕斯卡三角形（Zooko's Triangle）也称之为“Zooko's不可能三角”](https://en.wikipedia.org/wiki/Zooko%27s_triangle)的标识符公理：“有意义性（它使用人可记忆字符），去中心化（不需要一个中心授权机构），安全性（保证名称独特且唯一）—— 选择任意两个”。

当然，有很多用例，当从一个人友好的标识符（一个自然的语言名称、一个域名，或者DID 控制器的常规地址，或者一个移动电话号码、电子邮件地址、Twitter自定义链接或博客URL）的常规地址开始时，就可以发现一个这样的例子。然而，将人类友好的标识符映射到DID（并以一种可以被验证和信任的方式进行映射）的问题超出了这个规范的范围。

这个问题的解决方案（还有很多）应该在引用该规范的单独规范中定义。强烈建议这样的规范仔细考虑：（a）基于针对欺骗真实实体目标用户的人类友好标识符的大量安全攻击，以及（b）使用具有人类友好标识符的隐私后果，特别是如果它们是全局唯一的。

## 1.1.4 规范缘由

此规范的第一个目的是定义通用的DID方案和一组关于DID的通用操作集, 这些操作可以用于任何能够支持DID的分布式分类帐或网络。这个规范的第二个目的是定义一个DID方法规范的一致性要求 —— 一个单独的规范, 用于定义特定的DID方案和特定的分布式账本或网络的特定DID记录操作集。

从概念上讲，该规范与DID方法规范的关系类似于IETF通用URI规范（RFC3986）和特定的URI方案[IANA-URI-SCHEMES](https://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml)（如http:和https:是RFC7230中指定的方案）的关系。它也类似于IETF的通用URN规范（URN）和特定的URN名称空间定义（例如RFC4122中定义的UUID URN名称空间）的关系。不同之处在于，除了定义一个特定的DID方案之外，一个DID方法规范还必须指定读取、写入和撤销所写的网络上的记录的方法。

有关DID方法及其相应规范的列表, 请参阅[DID方法注册表（DID-METHOD-REGISTRY）](https://w3c-ccg.github.io/did-method-registry/)。

## 1.2 设计目标

本节总结了DID体系结构的设计目标和原则。

| 目标 | 描述 |
| --- | --- |
| Decentralization | DID architecture should eliminate the requirement for centralized authorities or single points of failure in identifier management, including the registration of globally unique identifiers, public verification keys, service endpoints, and other metadata. |
| Self‑Sovereignty | DID architecture should give entities, both human and non-human, the power to directly own and control their digital identifiers without the need to rely on external authorities. |
| Privacy | DID architecture should enable entities to control the privacy of their information, including minimal, selective, and progressive disclosure of attributes or other data. |
| Security | DID architecture should enable sufficient security for relying parties to depend on DID Documents for their required level of assurance. |
| Proof-based | DID architecture should enable an entity to provide cryptographic proof of authentication and proof of authorization rights. |
| Discoverability | DID architecture should make it possible for entities to discover DIDs for other entities to learn more about or interact with those entities. |
| Interoperability | DID architecture should use interoperable standards so DID infrastructure can make use of existing tools and software libraries designed for interoperability. |
| Portability | DID architecture should be system and network-independent and enable entities to use their digital identifiers with any system that supports DIDs and DID Methods. |
| Simplicity | To meet these design goals, DID architecture should be (to paraphrase Albert Einstein) "assimple as possible but no simpler". |
| Extensibility | When possible, DID architecture should enable extensibility provided it does not greatly hinder interoperability, portability, or simplicity. |

## 1.3 简单例子

这是DID的一个简单示例：

```
EXAMPLE 1: A simple example of a Decentralized Identifier (DID)

did:example:123456789abcdefghi
```

### 1.3.1 自我管理的DID文档

以下是描述上述DID的DID文档的示例。此示例假定控制此标识符的私钥的实体对DID文档具有权威性。

```
EXAMPLE 2: Minimal self-managed DID Document

{
  "@context": "https://w3id.org/did/v1",
  "id": "did:example:123456789abcdefghi",
  "publicKey": [{
    "id": "did:example:123456789abcdefghi#keys-1",
    "type": "RsaVerificationKey2018",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  }],
  "authentication": [{
    // this key can be used to authenticate as DID ...9938
    "type": "RsaSignatureAuthentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-1"
  }],
  "service": [{
    "type": "ExampleService",
    "serviceEndpoint": "https://example.com/endpoint/8377464"
  }]
}
```
# 2 术语

本文档尝试通过使用专门的术语来讨论特定概念，来传达分散标识符空间中概述的概念。此术语包含在下面，并链接到整个文档中，以帮助读者：

**分散标识符 (DID)**

DID是一种全局唯一标识符, 不需要集中的注册权限, 它是使用分布式分类帐技术或其他形式的分散网络注册的。在此规范之后定义了DID的通用格式，又在DID方法规范中定义了特定的DID方案。

**分散身份管理（Decentralized Identity Management）**

基于分散标识的身份管理。分散的身份管理扩展了标识符创建权限，超出了x.500目录服务、域名系统和大多数国家ID系统所需的传统信任的基础。

**DID文档（DID Document）**

描述DID的一组数据, 包括实体可以使用的机制，如公钥和匿名生物识别，该数据可以用作 did 进行身份验证。DID文档也可能包含描述该实体的其他属性或声明。这些文档是基于图形的数据结构，通常使用[JSON-LD](https://www.w3.org/TR/json-ld/)表达，但是可以使用其他兼容的基于图形的数据格式表示。

**DID片段（DID Fragment）**

第一个哈希符号字符 ("#") 后面的DID引用部分。一个DID片段使用与URI片段相同的语法。参见第5.5节。注意，一个片段必须立即跟随一个DID。如果一个DID引用包含一个DID路径后面跟着一个片段，那么这个片段不是一个DID片段。

**DID方法（DID Method）**

定义如何在特定的计划如何在一个特定的分布式账本或网络上实现，包括精确的方法，通过它，DID和DID文档可以被读取、写入和撤销。定义如何在特定的分布式账本或网络上实现特定的DID方案，包括可以读取，写入和撤销DID和DID文档的精确方法。

**DID路径（DID Path）**

第一个正向斜杠字符后面的DID引用的部分。DID路径使用相同的语法作为URI路径。见第5.4 节。请注意, 如果 did 路径后面跟一个片段, 则该片段不是DID片段。

**DID引用（DID Reference）**

DID加上一个可选的DID路径或DID片段。

**DID方案（DID Scheme）**

分散标识符的正式语法。本规范中定义了通用DID方案。DID方法规范定义了与特定DID方法一起使用的特定DID方案。

**分布式账本（Distributed Ledger (DLT)）**

一个分布式数据库，在这个数据库中，各个节点使用一致协议来维护一个共享的分布式账本，在这个分布式账本中，每个事务都被加密签名并被链接到以前的事务中。

**服务端点（Service Endpoint）**

服务代表实体运行的网络地址。特定服务的示例包括发现服务、社交网络、文件存储服务和可验证的声明存储库服务。服务端点也可以由广义数据交换协议 (如可扩展数据交换) 提供。

**可扩展数据交换（Extensible Data Interchange (XDI)）**

由[OASIS XDI技术委员会](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=xdi)定义的语义图格式和语义数据交换协议。

## 2.1 基本规范

此规范依赖于许多基本规范。依赖项及其用途如下所示。

[[JSON]]
The JSON specification provides the base data format that this specification uses.
[[JSON-LD]]
The JSON-LD specification enables the layering of data semantics on top of JSON data.

# 3. 分散标识符 (DID)

全局唯一分散标识符的概念并不是什么新概念；[全局唯一标识符(Universally Unique Identifiers,UUID)](https://en.wikipedia.org/wiki/Universally_unique_identifier)最初是在20世纪80年代开发的，后来成为开放软件基金会 [分布式计算环境（Distributed Computing Environment）](https://en.wikipedia.org/wiki/Distributed_Computing_Environment)的标准特性。UUID在没有集中注册中心服务的情况下实现全局唯一性，它使用了一种算法，该算法产生128位（128-bit）的值，并且具有足够的熵，以至于发生碰撞的几率非常小。UUID在RFC4122中被正式指定为统一资源名称(URN)的特定类型。

DID类似于UUID，除了: （a）像URL一样，它可以被解析或解引用到描述该实体的标准资源（参见第4部分文档），（b）与URL不同，DID文档通常包含加密材料，该加密材料允许对与DID相关联的实体进行认证。

## The Generic DID Scheme
The generic DID scheme is a URI scheme conformant with [[RFC3986]]. It consists of a DID followed by an optional path and/or fragment. The term DID refers only to the identifier conforming to the did rule in the ABNF below; when used alone, it does not include a path or fragment. A DID that may optionally include a path and/or fragment is called a DID reference.

Following is the ABNF definition using the syntax in [[RFC5234]] (which defines ALPHA as upper or lowercase A-Z).

```
did-reference      = did [ "/" did-path ] [ "#" did-fragment ]
did                = "did:" method ":" specific-idstring
method             = 1*methodchar
methodchar         = %x61-7A / DIGIT
specific-idstring  = idstring *( ":" idstring )
idstring           = 1*idchar
idchar             = ALPHA / DIGIT / "." / "-"

```

See Sections [](#paths)and [](#fragments)for the ABNF rules defining DID paths and fragments.

## Specific DID Method Schemes

A DID method specification MUST define exactly one specific DID scheme identified by exactly one method name (the method rule in Section [](#the-generic-did-scheme)). Since DIDs are intended for decentralized identity infrastructure, it is NOT RECOMMENDED to establish a registry of unique DID method names. Rather the uniqueness of DID method names should be established via human consensus, i.e., a specific DID scheme MUST use a method name that is unique among all DID method names known to the specification authors at the time of publication.

A list of known DID method names and their associated specifications is provided in Appendix [](#registries).

Since the method name is part of the DID, it SHOULD be as short as practical. A method name of five characters or less is RECOMMENDED. The method name MAY reflect the name of the distributed ledger or network to which the DID method specification applies.

The DID method specification for the specific DID scheme MUST specify how to generate the specific-idstring component of a DID. The specific-idstring value MUST be able to be generated without the use of a centralized registry service. The specific-idstring value SHOULD be globally unique by itself. The fully qualified DID as defined by the DID rule in Section [](#the-generic-did-scheme)MUST be globally unique.

If needed, a specific DID scheme MAY define multiple specific specific-idstring formats. It is RECOMMENDED that a specific DID scheme define as few specific-idstring formats as possible.

## Paths

A generic DID path (the did-path rule in Section [](#the-generic-did-scheme)) is identical to a URI path and MUST conform to the ABNF of the path-rootless ABNF rule in [[RFC3986]]. A DID path SHOULD be used to address resources available via a DID service endpoint. See Section [](#service-endpoints).

A specific DID scheme MAY specify ABNF rules for DID paths that are more restrictive than the generic rules in this section.

## Fragments

A generic DID fragment (the did-fragment rule in Section [](#the-generic-did-scheme)) is identical to a URI fragment and MUST conform to the ABNF of the fragment ABNF rule in [[RFC3986]]. A DID fragment MUST be used only as a method-independent pointer into the DID Document to identify a unique key description or other DID Document component. To resolve this pointer, the complete DID reference including the DID fragment MUST be used as the value of the id key for the target JSON object.

A specific DID scheme MAY specify ABNF rules for DID fragments that are more restrictive than the generic rules in this section.

## Normalization

For the broadest interoperability, DID normalization should be as simple and universal as possible. Therefore:

1.  The did: scheme name MUST be lowercase.
2.  The method name MUST be lowercase.
3.  Case sensitivity and normalization of the value of the specific-idstring rule in Section [](#the-generic-did-scheme)MUST be defined by the governing DID method specification.

## Persistence

A DID MUST be persistent and immutable, i.e., bound to an entity once and never changed (forever). Ideally a DID would be a completely abstract decentralized identifier (like a UUID) that could be bound to multiple underlying distributed ledgers or networks over time, thus maintaining its persistence independent of any particular ledger or network. However registering the same identifier on multiple ledgers or networks introduces extremely hard entityship and [start-of-authority](https://en.wikipedia.org/wiki/List_of_DNS_record_types%23SOA) (SOA) problems. It also greatly increases implementation complexity for developers.

To avoid these issues, it is RECOMMENDED that DID method specifications only produce DIDs and DID methods bound to strong, stable ledgers or networks capable of making the highest level of commitment to persistence of the DID and DID method over time.

NOTE: Although not included in this version, future versions of this specification may support a DID Document equivID property to establish verifiable equivalence relations between DID records representing the same entity on multiple ledgers or networks. Such equivalence relations can produce the practical equivalent of a single persistent abstract DID. See Future Work (Section [](#future-work)).

# DID Documents

If a DID is the index key in a key-value pair, then the DID Document is the value to which the index key points. The combination of a DID and its associated DID Document forms the root record for a decentralized identifier.

A DID Document MUST be a single JSON object conforming to [[RFC7159]]. For purposes of this version of the DID specification, the format of this JSON object is specified in [JSON-LD](https://json-ld.org/), a format for mapping JSON data into the RDF semantic graph model as defined by [[JSON-LD]]. Future versions of this specification MAY specify other semantic graph formats for a DID Document such as JXD (JSON XDI Data), a serialization format for the [XDI graph model](http://docs.oasis-open.org/xdi/xdi-core/v1.0/csd01/xdi-core-v1.0-csd01.xml).

The following sections define the properties of this JSON object, including whether these properties are required or optional.

## Context

JSON objects in JSON-LD format must include a JSON-LD context statement. The rules for this statement are:

1.  A DID Document MUST have exactly one top-level context statement.
2.  The key for this property MUST be `@context`.
3.  The value of this key MUST be the URL for the generic DID context: `https://w3id.org/did/v1`.

Example (using an example URL):

```
{
  "@context": "https://w3id.org/did/v1"
}
```

DID method specifications MAY define their own JSON-LD contexts. However it is NOT RECOMMENDED to define a new context unless necessary to properly implement the method. Method-specific contexts MUST NOT override the terms defined in the generic DID context.

## DID Subject

The DID subject is the identifier that the DID Document is about, i.e., it is the DID described by DID Document. The rules for a DID subject are:

1.  A DID Document MUST have exactly one DID subject.
2.  The key for this property MUST be id.
3.  The value of this key MUST be a valid DID.
4.  When this DID Document is registered with the target distributed ledger or network, the registered DID MUST match this DID subject value.

Example:

```
{
  "id": "did:example:21tDAKCERh95uGgKbJNHYp"
}

```

>DID Method specifications MAY create intermediate representations of a DID Document that do not contain the `id` key, such as when a DID Resolver is performing resolution. However, the fully resolved DID Document MUST contain a valid `id` property.

## Public Keys

Public keys are used for digital signatures, encryption and other cryptographic operations, which in turn are the basis for purposes such as authentication (see Section [](#authentication)) or establishing secure communication with service endpoints (see Section [](#service-endpoints)). In addition, public keys may play a role in authorization mechanisms of DID CRUD operations (see Section [](#did-operations)); This may be defined by DID Method specifications.

If a public key does not exist in the DID Document, it MUST be assumed the key has been revoked or is invalid. The DID Document MAY contain revoked keys. A DID Document that contains a revoked key MUST also contain or refer to the revocation information for the key (e.g., a revocation list). Each DID Method specification is expected to detail how revocation is performed and tracked.

The rules for public keys are:

1.  A DID Document MAY include a `publicKey` property.
2.  The value of the `publicKey` property should be an array of public keys.
3.  Each public key must include `id` and `type` properties, and exactly one value property.
4.  Each public key may include an `controller` property, which identifies the entity that controls the corresponding private key. If this property is missing, it is assumed to be the DID subject.
5.  The value property of a public key may be `publicKeyPem`, `publicKeyJwk`, `publicKeyHex`, `publicKeyBase64`or similar, depending on the format and encoding of the public key.
6.  A registry of key types and formats is available in Appendix [](#registries).

Example:

```
{
  "@context": ["https://w3id.org/did/v1", "https://w3id.org/security/v1"],
  "id": "did:example:123456789abcdefghi",
  ...
  "publicKey": [{
    "id": "did:example:123456789abcdefghi#keys-1",
    "type": "RsaVerificationKey2018",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  }, {
    "id": "did:example:123456789abcdefghi#keys-2",
    "type": "Ed25519VerificationKey2018",
    "controller": "did:example:pqrstuvwxyz0987654321",
    "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
  }, {
    "id": "did:example:123456789abcdefghi#keys-3",
    "type": "Secp256k1VerificationKey2018",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyHex": "02b97c30de767f084ce3080168ee293053ba33b235d7116a3263d29f1450936b71"
  }],
  ...
}
```

A key may be *embedded* or *referenced* in a DID Document. For example, the `authentication` property may refer to keys in both ways:

```
{
...

  "authentication": [{
    // this key is referenced
    "type": "RsaSignatureAuthentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-1"
  }, {
    // this key is embedded
    "type": "RsaSignatureAuthentication2018",
    "publicKey: {
      "id": "did:example:123456789abcdefghi#keys-2",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:example:pqrstuvwxyz0987654321",
      "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  }],

...
}
```


The algorithm to use when processing a `publicKey` property in a DID Document is:

1.  Let *value* be the data associated with the `publicKey` property and initialize *result* to `null`.
2.  If *value* is an object, the key material is embedded. Set *result* to *value*.
3.  If *value* is a string, the key is included by reference. Assume *value* is a URL.
   >1.  Dereference the URL and retrieve the `publicKey` properties associated with the URL (e.g., process the `publicKey` property at the top-level of the dereferenced document).
   >2.  Iterate through each public key object.
        1.  If the `id` property of the object matches *value*, set *result* to the object.
4.  If *result* does not contain at least the `id`, `type`, and `controller` properties as well as any mandatory public cryptographic material, as determined by the *result*'s `type` property, throw an error.

>While the `controller` field may seem redundant in some of the examples above, keys may be expressed in a DID Document where the controller is described in another DID Document. Linked Data Proof libraries typically expect the `controller` field to always exist and may throw an exception if it is missing. Futhermore, per the requirement that DID Documents be interpretable as either a graph or a tree, a default `controller` field cannot be inferred by using a key's position in a tree.

>Caching and expiration of the keys in a DID Document is entirely the responsibility of DID resolvers and other clients. See Section [](#did-resolvers).

## Authentication

Authentication is the mechanism by which an entity can cryptographically prove that they are associated with a DID and DID Description. See Section [](#binding-of-identity). Note that Authentication is separate from Authorization because an entity may wish to enable other entities to update the DID Document (for example, to assist with key recovery as discussed in Section [](#key-revocation-and-recovery)) without enabling them to prove control (and thus be able to impersonate the entity).

The rules for Authentication are:

1.  A DID Document MAY include an `authentication` property.
2.  The value of the `authentication` property should be an array of proof mechanisms.
3.  Each proof mechanism must include the `type` property.
4.  Each proof mechanism MAY embed or reference a public key (see Section [](#public-keys)).

Example:

```
{
  "@context": "https://w3id.org/did/v1",
  "id": "did:example:123456789abcdefghi",
  ...
  "authentication": [{
    // this key can be used to authenticate as DID ...fghi
    "type": "RsaSignatureAuthentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-1"
  }, {
    // this key can be used to authenticate as DID ...fghi
    "type": "PseudonymousBiometricAuthentication2018",
    "biometricTemplate": "did:example:123456789abcdefghi#bio-1"
  }],
  ...
}
```

## Authorization and Delegation

Authorization is the mechanism by which an entity states how one may perform operations on behalf of the entity. Delegation is the mechanism that an entity may use to authorize other entities to act on its behalf. Note that Authorization is separate from Authentication as explained in Section [](#authentication). This is particularly important for key recovery in the case of key loss, when the entity no longer has access to their keys, or key compromise, where the controller’s trusted third parties need to override malicious activity by an attacker. See Section [](#security-considerations).

Since Authorization and Delegation are typically implemented by the ledger, each DID Method specification is expected to detail how authorization and delegation are performed for the ledger.

There are at least two suggested methods for implementing Authorization, Delegation, and the concept of Guardianship:

1.  A ledger could implement a coarse grained `guardian` pattern by re-using the same cryptography suite pattern used by the `authentication` property, or more preferably
2.  A ledger could implement a Capabilities-based approach and provide more fine-grained control of authorization, delegation, and guardianship.

## Service Endpoints

In addition to publication of authentication and authorization mechanisms, the other primary purpose of a DID Document is to enable discovery of service endpoints for the entity. A service endpoint may represent any type of service the entity wishes to advertise, including decentralized identity management services for further discovery, authentication, authorization, or interaction. The rules for service endpoints are:

1.  A DID Document MAY include a `service` property.
2.  The value of the `service` property should be an array of service endpoints.
3.  Each service endpoint must include `id`, `type`, and `serviceEndpoint` properties, and MAY include additional properties.
4.  The service endpoint protocol SHOULD be published in an open standard specification.
5.  The value of the `serviceEndpoint` property MUST be a JSON-LD object or a valid URI conforming to [[RFC3986]] and normalized according to the rules in section 6 of [[RFC3986]] and to any normalization rules in its applicable URI scheme specification.

Example:

```
{
  "service": [{
    "id": "did:example:123456789abcdefghi;openid",
    "type": "OpenIdConnectVersion1.0Service",
    "serviceEndpoint": "https://openid.example.com/"
  }, {
    "id": "did:example:123456789abcdefghi;vcr",
    "type": "CredentialRepositoryService",
    "serviceEndpoint": "https://repository.example.com/service/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;xdi",
    "type": "XdiService",
    "serviceEndpoint": "https://xdi.example.com/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;agent",
    "type": "AgentService",
    "serviceEndpoint": "https://agent.example.com/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;hub",
    "type": "HubService",
    "serviceEndpoint": "https://hub.example.com/.identity/did:example:0123456789abcdef/"
  }, {
    "id": "did:example:123456789abcdefghi;messages",
    "type": "MessagingService",
    "serviceEndpoint": "https://example.com/messages/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;inbox",
    "type": "SocialWebInboxService",
    "serviceEndpoint": "https://social.example.com/83hfh37dj",
    "description": "My public social inbox",
    "spamCost": {
      "amount": "0.50",
      "currency": "USD"
    }
  }, {
    "id": "did:example:123456789abcdefghi;authpush",
    "type": "DidAuthPushModeVersion1",
    "serviceEndpoint": "http://auth.example.com/did:example:123456789abcdefg"
  }]
}
```

See Sections [](#specific-did-method-schemes)and [](#authentication)for further security considerations regarding authentication service endpoints.

## Created (Optional)

Standard metadata for identifier records includes a timestamp of the original creation. The rules for including a creation timestamp are:

1.  A DID Document MUST have zero or one property representing a creation timestamp. It is RECOMMENDED to include this property.
2.  The key for this property MUST be created.
3.  The value of this key MUST be a valid XML datetime value as defined in section 3.3.7 of [W3C XML Schema Definition Language (XSD) 1.1 Part 2: Datatypes](https://www.w3.org/TR/xmlschema11-2/) [[XMLSCHEMA11-2]].
4.  This datetime value MUST be normalized to UTC 00:00 as indicated by the trailing "Z".
5.  Method specifications that rely on DLTs SHOULD require time values that are after the known ["median time past" (defined in Bitcoin BIP 113)](https://github.com/bitcoin/bips/blob/master/bip-0113.mediawiki), when the DLT supports such a notion.

Example:

```
{
  "created": "2002-10-10T17:00:00Z"
}
```

## Updated (Optional)

Standard metadata for identifier records includes a timestamp of the most recent change. The rules for including an updated timestamp are:

1.  A DID Document MUST have zero or one property representing an updated timestamp. It is RECOMMENDED to include this property.
2.  The key for this property MUST be updated.
3.  The value of this key MUST follow the formatting rules (3, 4, 5) from section [](#created-optional).

Example:

```
{
  "updated": "2016-10-17T02:41:00Z"
}
```

## Proof (Optional)

A `proof` on a DID Document is cryptographic proof of the integrity of the DID Document according to either:

1.  The entity as defined in section [](#service-endpoints), or if not present:
2.  The delegate as defined in section 4.3.

This proof is NOT proof of the binding between a DID and a DID Document. See Section [](#binding-of-identity). The rules for a proof are:

1.  A DID Document MAY have exactly one property representing a proof.
2.  The key for this property MUST be `proof`.
3.  The value of this key MUST be a valid JSON-LD proof as defined by [Linked Data Proofs](https://w3c-dvcg.github.io/ld-signatures/).

Example:

```
{
  "proof": {
    "type": "LinkedDataSignature2015",
    "created": "2016-02-08T16:02:20Z",
    "creator": "did:example:8uQhQMGzWxR8vw5P3UWH1ja#keys-1",
    "signatureValue": "QNB13Y7Q9...1tzjn4w=="
  }
}
```


## Extensibility

One of the goals of the Decentralized Identifiers Data Model is to enable permissionless innovation. This requires that the data model is extensible in a number of different ways:

*   The requirement to model complex multi-entity relationships is provided through the use of a graph-based data model.
*   The requirement to enable extending the machine-readable vocabularies used to describe information in the data model — without relying on a centralized system — is accomplished via the use of [[LINKED-DATA]].
*   The requirement to support multiple types of cryptographic proof formats is accomplished via the use of Linked Data Proofs [[LD-PROOFS]], Linked Data Signatures [[LD-SIGNATURES]], and a variety of signature suites.
*   The requirement to provide all of the extensibility mechanisms outlined above in a data format that is popular among software developers and web page authors is enabled via the use of [[!JSON-LD]].

This approach to data modeling is often called an "open world assumption", meaning that any entity can say anything about any other entity. This approach often feels in conflict with building simple and predictable software systems. Balancing extensibility with program correctness is always more challenging with an open world assumption than it is with closed software systems.

The rest of this section describes how both extensibility and program correctness are achieved through a series of examples.

Let us assume that we start with the following DID Document:

```
{
  "@context": "https://example.org/example-method/v1",
  "id": "did:example:123456789abcdefghi",
  "publicKey": [{ ... }],
  "authentication": [{ ... }],
  "service": [{ ... }]
}
```

The contents of the `publicKey`, `authentication`, and `service` properties are not important for the purposes of this section. What is important is that the object above is a valid DID Document. Let's assume that a developer wanted to extend the DID Document to express an additional piece of information: the subject's public photo stream.

The first thing that a developer would do is create a JSON-LD Context containing the new term:

```
{
  "@context": {
    "PhotoStreamService": "https://example.com/vocab#PhotoStreamService"
  }
}
```

Now that the JSON-LD Context has been created, the developer MUST publish it somewhere that is accessible to any DID Document processor. For this example, let us assume that the JSON-LD Context above is published in the decentralized ledger at the following URL: `did:example:contexts:987654321`. At this point, extending the first example in this section is a simple matter of including the context above and adding the new property to the DID Document.

```
{
  "@context": "https://example.org/example-method/v1",
  "id": "did:example:123456789abcdefghi",
  "authentication": [{ ... }],
  "service": [{
    "@context": "did:example:contexts:987654321",
    "id": "did:example:123456789abcdefghi;photos",
    "type": "PhotoStreamService",
    "serviceEndpoint": "https://example.org/photos/379283"
  }]
}
      
```

The examples so far have shown that it is easy to extend the Decentralized Identifiers Data Model in a permissionless and decentralized way. The mechanism also ensures that Decentralized Identifiers created in this way prevent namespace conflicts and semantic ambiguity.

An extensibility model that is this dynamic does increase implementation burden. Software written for such a system will have to determine if accepting DID Documents with extensions is acceptable based on the risk profile of the application. Some applications may choose to accept but ignore extensions, others may choose to only accept certain extensions, while highly secure environments may disallow extensions. These decisions are up to the application developers and are specifically not the domain of this specification.

Implementations MUST produce an error when an extension JSON-LD Context overrides the expanded URL for a term specified in this specification. To avoid the possibility of accidentally overriding terms, developers are urged to scope their extensions. For example, the following extension scopes the new`PhotoStreamService` term so that it may only be used within the `service` property:

```
{
  "@context": {
    "service": {
      "@id": "https://w3id.org/did#service",
      "@context": {
        "PhotoStreamService": "https://example.com/vocab#PhotoStreamService"
      }
    }
  }
}
    
```

Developers are urged to ensure that extension JSON-LD Contexts are highly available. Implementations that cannot fetch a context will produce an error. Strategies for ensuring that extension JSON-LD Contexts are always available include using content-addressed URLs for contexts, bundling context documents with implementations, or enabling aggressive caching of contexts.

# DID Operations

To enable the full functionality of DIDs and DID Documents on a particular distributed ledger or network (called the target system), a DID method specification MUST specify how each of the following[CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) operations is performed by a client. Each operation MUST be specified to the level of detail necessary to build and test interoperable client implementations with the target system. Note that, due to the specified contents of DID Documents, these operations can effectively be used to perform all the operations required of a CKMS (cryptographic key management system), e.g.:

*   Key registration
*   Key replacement
*   Key rotation
*   Key recovery
*   Key expiration

## Create

The DID method specification MUST specify how a client creates a DID record—the combination of a DID and its associated DID Document—on the target system, including all cryptographic operations necessary to establish proof of control.

## Read/Verify

The DID method specification MUST specify how a client uses a DID to request a DID Document from the target system, including how the client can verify the authenticity of the response.

## Update

The DID method specification MUST specify how a client can update a DID record on the target system, including all cryptographic operations necessary to establish proof of control.

## Delete/Revoke

Although a core feature of distributed ledgers is immutability, the DID method specification MUST specify how a client can revoke a DID record on the target system, including all cryptographic operations necessary to establish proof of revocation.

# DID Resolvers

A DID resolver is a software component with an API designed to accept requests for DID lookups and execute the corresponding DID method to retrieve the authoritative DID Document. To be conformant with this specification, a DID resolver:

1.  SHOULD validate that a DID is valid according to its DID method specification, otherwise it should produce an error.
2.  MUST conform to the requirements of the applicable DID method specification when performing DID resolution operations.
3.  SHOULD offer the service of verifying the integrity of the DID Document if it is signed.
4.  MAY offer the service of returning requested properties of the DID Document.

# Security Considerations

NOTE TO IMPLEMENTERS: During the Implementer’s Draft stage, this section focuses on security topics that should be important in early implementations. The editors are also seeking feedback on threats and threat mitigations that should be reflected in this section or elsewhere in the spec. As the root identifier records for decentralized identifiers, DIDs and DID Documents are a vital component of decentralized identity management. They are also the foundational building blocks of DPKI (decentralized public key infrastructure) as an augmentation to conventional X.509 certificates. As such, DIDs are designed to operate under the general Internet threat model used by many IETF standards. We assume uncompromised endpoints, but allow messages to be read or corrupted on the network. Protecting against an attack when a system is compromised requires external key-signing hardware. See also section [](#key-revocation-and-recovery)regarding key revocation and recovery. For their part, the DLTs hosting DIDs and DID Documents have special security properties for preventing active attacks. Their design uses public/private key cryptography to allow operation on passively monitored networks without risking compromise of private keys. This is what makes DID architecture and decentralized identity possible.

## Requirements of DID Method Specifications

1.  DID method specifications MUST include their own Security Considerations sections.
2.  This section MUST consider all the requirements mentioned in section 5 of [[RFC3552]] (page 27) for the DID operations defined in the specification. In particular:

>Discussions at Rebooting the Web of Trust 5 resulted in consensus to move Authorization to DID Method specifications. It is currently expected that there will be an attempt to create a generalized authorization mechanism that is build on object capabilities.

At least the following forms of attack MUST be considered: eavesdropping, replay, message insertion, deletion, modification, and man-in-the-middle. Potential denial of service attacks MUST be identified as well. If the protocol incorporates cryptographic protection mechanisms, it should be clearly indicated which portions of the data are protected and what the protections are (i.e., integrity only, confidentiality, and/or endpoint authentication, etc.). Some indication should also be given to what sorts of attacks the cryptographic protection is susceptible. Data which should be held secret (keying material, random seeds, etc.) should be clearly labeled. If the technology involves authentication, particularly user-host authentication, the security of the authentication method MUST be clearly specified.

3.  This section MUST also discuss, per Section 5 of [[RFC3552]], residual risks (such as the risks from compromise in a related protocol, incorrect implementation, or cipher) after threat mitigation has been deployed.
4.  This section MUST provide integrity protection and update authentication for all operations required by Section 7 of this specification (DID Operations).
5.  Where DID methods make use of peer-to-peer computing resources (such as with all known DLTs), the expected burdens of those resources SHOULD be discussed in relation to denial of service.
6.  Method-specific endpoint authentication MUST be discussed. Where DID methods make use of DLTs with varying network topology, sometimes offered as "light node" or “ [thin client](https://en.bitcoin.it/wiki/Thin_Client_Security) ” implementations to reduce required computing resources, the security assumptions of the topology available to implementations of the DID method MUST be discussed.
7.  DID methods MUST discuss the policy mechanism by which DIDs are proven to be uniquely assigned. A DID fits the functional definition of a URN as defined in [[RFC2141]]—a persistent identifier that is assigned once to a resource and never reassigned. In a security context this is particularly important since a DID may be used to identify a specific party subject to a specific set of authorization rights.
8.  DID methods that introduce new authentication service endpoint types (Section [](#service-endpoints)) SHOULD consider the security requirements of the supported authentication protocol.

## Binding of Identity

### Proving Control of a DID and DID Document

Signatures are one method to allow DID Documents to be cryptographically verifiable.

By itself, a verified signature on a self-signed DID Document does not prove control of a DID. It only proves the following:

1.  The DID Document has not been tampered with since it was registered.
2.  The controller of the DID Document controlled the private key used for the signature at the time the signature was generated.

Proving control of a DID, i.e., the binding between the DID and the DID Document that describes it, requires a two step process:

1.  Resolving the DID to a DID Document according to its DID method specification.
2.  Verifying that the id property of the resulting DID Document matches the DID that was resolved.

It should be noted that this process proves control of a DID and DID Document regardless of whether the DID Document is signed.

Signatures on DID Documents are optional. DID Method Specs SHOULD explain and specify their implementation if applicable.

It is RECOMMENDED to combine timestamps with signatures.

### Proving Control of a Public Key

There are two methods for proving control of the private key corresponding to a public key description in the DID Document: static and dynamic. The static method is to sign the DID Document with the private key. This proves control of the private key at a time no later than the DID Document was registered. If the DID Document is not signed, control of a public key described in the DID Document may still be proven dynamically as follows:

1.  Send a challenge message containing a public key description from the DID Document and a nonce to an appropriate service endpoint described in the DID Document.
2.  Verify the signature of the response message against the public key description.

### Authentication and Verifiable Claims

A DID and DID Document do not inherently carry any [PII](https://en.wikipedia.org/wiki/Personally_identifiable_information) (personally-identifiable information). The process of binding a DID to a real-world entity such as a person or company, for example with credentials whose subject is that DID, is out of scope for this specification. However this topic is the focus of the [verifiable claims](https://w3c.github.io/vctf/) standardization work at the W3C (where the term "DID" originated).

## Authentication Service Endpoints

If a DID Document publishes a service endpoint intended for authentication or authorization of an entity (section [](#service-endpoints)), it is the responsibility of the service endpoint provider, entity, and/or relying party to comply with the requirements of the authentication protocol(s) supported at that service endpoint.

## Non-Repudiation

Non-repudiation of DIDs and DID Document updates is supported under the assumption that: (1) the entity is monitoring for unauthorized updates (see Section [](#notification-of-did-document-changes)) and (2) the entity has had adequate opportunity to revoke malicious updates according to the DID method's access control mechanism (section [](#authentication)). This capability is further supported if timestamps are included (sections [](#created-optional)and [](#updated-optional)) and the target DLT system supports timestamps.

## Notification of DID Document Changes

One mitigation against unauthorized changes to a DID Document is monitoring and actively notifying the entity when there are changes. This is analogous to helping prevent account takeover on conventional username/password accounts by sending password reset notifications to the email addresses on file. In the case of a DID, where there is no intermediary registrar or account provider to generate the notification, the following approaches are RECOMMENDED:

1.  Subscriptions. If the ledger or network on which the DID is registered directly supports change notifications, this service can be offered to DID controllers. Notifications may be sent directly to the relevant service endpoints listed in an existing DID.
2.  Self-monitoring. An entity may employ its own local or online agent to periodically monitor for changes to a DID Document.
3.  Third-party monitoring. An entity may rely on a third party monitoring service, however this introduces another vector of attack.

## Key and Signature Expiration

In a decentralized identifier architecture, there are no centralized authorities to enforce key or signature expiration policies. Therefore DID resolvers and other client applications SHOULD validate that keys were not expired at the time they were used. Since some use cases may have legitimate reasons why already-expired keys can be extended, a key expiration SHOULD NOT prevent any further use of the key, and implementations of a resolver SHOULD be compatible with such extension behavior.

## Key Revocation and Recovery

Section [](#did-operations)specifies the DID operations that must be supported by a DID method specification, including revocation of a DID Document by replacing it with an updated DID Document. In general, checking for key revocation on DLT-based methods is expected to be handled in a manner similar to checking the balance of a cryptocurrency account on a distributed ledger: if the balance is empty, the entire DID is revoked. DID method specifications SHOULD enable support for a quorum of trusted parties to enable key recovery. Some of the facilities to do so are suggested in section 6.5, Authorization. Note that not all DID method specifications will recognize control from DIDs registered using other DID methods and they MAY restrict third-party control to DIDs that use the same method. Access control and key recovery in a DID method specification MAY also include a time lock feature to protect against key compromise by maintaining a second track of control for recovery. Further specification of this type of control is a matter for future work (see section [](#time-locks-and-did-document-recovery)).

# Privacy Considerations

It is critically important to apply the principles of Privacy by Design to all aspects of decentralized identifier architecture, because DIDs and DID Documents are—by design—administered directly by their controllers. There is no registrar, hosting company, or other intermediate service provider to recommend or apply additional privacy safeguards. The authors of this specification have applied all seven Privacy by Design principles throughout its development. For example, privacy in this specification is preventative not remedial, and privacy is an embedded default. Furthermore, decentralized identifier architecture by itself embodies principle #7, "Respect for user privacy—keep it user-centric." This section lists additional privacy considerations that implementers, delegates, and entities should bear in mind.

## Requirements of DID Method Specifications

1.  DID method specifications MUST include their own Privacy Considerations sections, if only to point to the general privacy considerations in this section.
2.  The DID method privacy section MUST discuss any subsection of section 5 of [[RFC6973]] that could apply in a method-specific manner. The subsections to consider are: surveillance, stored data compromise, unsolicited traffic, misattribution, correlation, identification, secondary use, disclosure, exclusion.

## Keep Personally-Identifiable Information (PII) Off-Ledger

If a DID method specification is written for a public ledger or network where all DIDs and DID Documents will be publicly available, it is STRONGLY RECOMMENDED that DID Documents contain no PII. All PII should be kept off-ledger behind service endpoints under the control of the entity. With this privacy architecture, PII may be exchanged on a private, peer-to-peer basis using communications channels identified and secured by key descriptions in DID records. This also enables entities and relying parties to implement the [GDPR](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) [right to be forgotten](https://en.wikipedia.org/wiki/Right_to_be_forgotten), as no PII will be written to an immutable ledger.

## DID Correlation Risks and Pseudonymous DIDs

Like any type of globally unique identifier, DIDs may be used for correlation. DID controllers can mitigate this privacy risk by using pairwise unique DIDs, i.e., by sharing a different private DID for every relationship. In effect, each DID acts as a pseudonym. A pseudonymous DID need only be shared with more than one party when the entity explicitly authorizes correlation between those parties. If pseudonymous DIDs are the default, then the only need for a public DID—a DID published openly or shared with a large number of parties—is when the entity explicitly desires public identification.

## DID Document Correlation Risks

The anti-correlation protections of pseudonymous DIDs are easily defeated if the data in the corresponding DID Documents can be correlated. For example, using same public key descriptions or bespoke service endpoints in multiple DID Documents can provide as much correlation information as using the same DID. Therefore the DID Document for a pseudonymous DID SHOULD also use pairwise-unique public keys. It might seem natural to also use pairwise-unique service endpoints in the DID Document for a pseudonymous DID. However, unique endpoints allow all traffic between to DIDs to be isolated perfectly into unique buckets, where timing correlation and similar analysis is easy. Therefore, a better strategy for endpoint privacy may be to share an endpoint among thousands or millions of DIDs owned by many different entities.

## Herd Privacy

When an entity is indistinguishable from others in the herd, privacy is available. When the act of engaging privately with another party is by itself a recognizable flag, privacy is greatly diminished. DIDs and DID methods SHOULD work to improve herd privacy, particularly for those who legitimately need it most. Choose technologies and human interfaces that default to preserving anonymity and pseudonymity. In order to reduce [digital fingerprints](https://en.wikipedia.org/wiki/Device_fingerprint), share common settings across client implementations, keep negotiated options to a minimum on wire protocols, use encrypted transport layers, and pad messages to standard lengths.

# Future Work

## Upper Limits on DID Character Length

The current specification does not take a position on maximum length of a DID. The maximum interoperable URL length is currently about 2K characters. QR codes can handle about 4K characters. Clients using DIDs will be responsible for storing many DIDs, and some methods would be able to externalize some of their costs onto clients by relying on more complicated signature schemes or by adding state into DIDs intended for temporary use. A future version of this specification should set reasonable limits on DID character length to minimize externalities.

## Equivalence

Including an equivalence property, such as equivID, in DID Documents whose value is an array of DIDs would allow entities to assert two or more DIDs that represent the same entity. This capability has numerous uses, including supporting migration between ledgers and providing forward compatibility of existing DIDs to future DLTs. In theory, equivalent DIDs should have the same identifier rights, allowing [verifiable claims](https://w3c.github.io/vctf/) made against one DID to apply to equivalent DIDs. Equivalence was not included in the current specification due to the complexity of verifying equivalence across different DLTs and different DID methods, and also of aggregating properties of equivalent DID Documents. However equivalence should be supported in a future version of this specification.

## Timestamps

Verifiable timestamps have significant utility for identifier records. This is a good fit for DLTs, since most offer some type of timestamp mechanism. Despite some transactional cost, they are the most censorship-resistant transaction ordering systems in the world, so they are nearly ideal for DID Document timestamping. In some cases a DLT's immediate timing is approximate, however their sense of ["median time past" (see Bitcoin BIP 113)](https://github.com/bitcoin/bips/blob/master/bip-0113.mediawiki%23Abstract) can be precisely defined. A generic DID Document timestamping mechanism could would work across all DLTs and might operate via a mechanism including either individual transactions or transaction batches. The generic mechanism was deemed out of scope for this version, although it may be included in a future version of this specification.

## Time Locks and DID Document Recovery

Section [](#key-revocation-and-recovery)mentions one possible clever use of time locks to recover control of a DID after a key compromise. The technique relies on an ability to override the most recent update to a DID Document with Authorization applied by an earlier version of the DID Document in order to defeat the attacker. This protection depends on adding a [time lock (see Bitcoin BIP 65)](https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki%23Abstract) to protect part of the transaction chain, enabling a Authorization block to be used to recover control. We plan to add support for time locks in a future version of this specification.

## Smart Signatures

Not all DLTs can support the Authorization logic in section 6.5\. Therefore, in this version of the specification, all Authorization logic must be delegated to DID method specifications. A potential future solution is a [Smart Signature](http://www.weboftrust.info/downloads/smart-signatures.pdf) specification that specifies the code any conformant DLT may implement to process signature control logic.

## Verifiable Claims

Although DIDs and DID Documents form a foundation for decentralized identity, they are only the first step in describing an entity. The rest of the descriptive power comes through collecting and selectively using [verifiable claims](https://w3c.github.io/vctf/). Future versions of the specification will describe in more detail how DIDs and DID Document can be integrated with—and help enable—the verifiable claims ecosystem.

## Alternate Serializations and Graph Models

This version of the specification relies on JSON-LD and the RDF graph model for expressing a DID Document. Future versions of this specification MAY specify other semantic graph formats for a DID Document, such as JXD (JSON XDI Data), a serialization format for the XDI graph model as defined by the [OASIS XDI Core 1.0 specification](http://docs.oasis-open.org/xdi/xdi-core/v1.0/csd01/xdi-core-v1.0-csd01.xml).

# A. 注册

有多个注册中心定义了DID规范的方法和扩展。这些注册中心是：

| 注册 | 作用 |
| --- | --- |
| [DID Method Registry](https://w3c-ccg.github.io/did-method-registry/) | Defines all known DID Methods and contains links to their specifications. |
| [Linked Data Cryptography Suite Registry](https://w3c-ccg.github.io/ld-cryptosuite-registry/) | Defines all known Linked Data Cryptography Suites and Key Formats. |

# B. 实例研究

下面提供了面向未来的真实环境:

```
{
  "@context": "https://w3id.org/future-method/v1",
  "id": "did:example:123456789abcdefghi",

  "publicKey": [{
    "id": "did:example:123456789abcdefghi#keys-1",
    "type": "RsaVerificationKey2018",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  }, {
    "id": "did:example:123456789abcdefghi#keys-2",
    "type": "Ed25519VerificationKey2018",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
  }, {
    "id": "did:example:123456789abcdefghi#keys-3",
    "type": "RsaPublicKeyExchangeKey2018",
    "controller": "did:example:123456789abcdefghi",
    "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  }],

  "authentication": [{
    // this mechanism can be used to authenticate as DID ...fghi
    "type": "RsaSignatureAuthentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-1"
  }, {
    // this mechanism can be used to biometrically authenticate as DID ...fghi
    "type": "ieee2410Authentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-2"
  }],

  "service": [{
    "type": "OpenIdConnectVersion1.0Service",
    "serviceEndpoint": "https://openid.example.com/"
  }, {
    "type": "CredentialRepositoryService",
    "serviceEndpoint": "https://repository.example.com/service/8377464"
  }, {
    "type": "XdiService",
    "serviceEndpoint": "https://xdi.example.com/8377464"
  }, {
    "type": "HubService",
    "serviceEndpoint": "https://hub.example.com/.identity/did:example:0123456789abcdef/"
  }, {
    "type": "MessagingService",
    "serviceEndpoint": "https://example.com/messages/8377464"
  }, {
    "type": "SocialWebInboxService",
    "serviceEndpoint": "https://social.example.com/83hfh37dj",
    "description": "My public social inbox",
    "spamCost": {
      "amount": "0.50",
      "currency": "USD"
    }
  }, {
    "type": "DidAuthPushModeVersion1",
    "serviceEndpoint": "http://auth.example.com/did:example:123456789abcdefghi"
  }, {
    "id": "did:example:123456789abcdefghi;bops",
    "type": "BopsService",
    "serviceEndpoint": "https://bops.example.com/enterprise/"
  }]
}
```

# C. 参考

## C.1 参考文献

**[DID-METHOD-REGISTRY]**

The Decentralized Identifier Method Registry. Manu Sporny; Drummond Reed. Digital Verification Community Group. CG-DRAFT. URL: https://w3c-ccg.github.io/did-method-registry/

**[IANA-URI-SCHEMES]**

Uniform Resource Identifier (URI) Schemes. IANA. URL: https://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml

**[JSON]**

The application/json Media Type for JavaScript Object Notation (JSON). D. Crockford. IETF. July 2006. Informational. URL: https://tools.ietf.org/html/rfc4627

**[JSON-LD]**

JSON-LD 1.0. Manu Sporny; Gregg Kellogg; Markus Lanthaler. W3C. 16 January 2014. W3C Recommendation. URL: https://www.w3.org/TR/json-ld/

**[LD-PROOFS]**

Linked Data Proofs. Manu Sporny; Dave Longley. Digital Verification Community Group. CG-DRAFT. URL: https://w3c-dvcg.github.io/ld-proofs/

**[LD-SIGNATURES]**

Linked Data Signatures. Manu Sporny; Dave Longley. Digital Verification Community Group. CG-DRAFT. URL: https://w3c-dvcg.github.io/ld-signatures/

**[LINKED-DATA]**

Linked Data Design Issues. Tim Berners-Lee. W3C. 27 July 2006. W3C-Internal Document. URL: https://www.w3.org/DesignIssues/LinkedData.html

**[RFC3552]**

Guidelines for Writing RFC Text on Security Considerations. E. Rescorla; B. Korver. IETF. July 2003. Best Current Practice. URL: https://tools.ietf.org/html/rfc3552

**[RFC4122]**

A Universally Unique IDentifier (UUID) URN Namespace. P. Leach; M. Mealling; R. Salz. IETF. July 2005. Proposed Standard. URL: https://tools.ietf.org/html/rfc4122

**[RFC5234]**

Augmented BNF for Syntax Specifications: ABNF. D. Crocker, Ed.; P. Overell. IETF. January 2008. Internet Standard. URL: https://tools.ietf.org/html/rfc5234

**[RFC6973]**

Privacy Considerations for Internet Protocols. A. Cooper; H. Tschofenig; B. Aboba; J. Peterson; J. Morris; M. Hansen; R. Smith. IETF. July 2013. Informational. URL: https://tools.ietf.org/html/rfc6973

**[RFC7159]**

The JavaScript Object Notation (JSON) Data Interchange Format. T. Bray, Ed.. IETF. March 2014. Proposed Standard. URL: https://tools.ietf.org/html/rfc7159

**[RFC7230]**

Hypertext Transfer Protocol (HTTP/1.1): Message Syntax and Routing. R. Fielding, Ed.; J. Reschke, Ed.. IETF. June 2014. Proposed Standard. URL: https://tools.ietf.org/html/rfc7230

**[URI]**

Uniform Resource Identifier (URI): Generic Syntax. T. Berners-Lee; R. Fielding; L. Masinter. IETF. January 2005. Internet Standard. URL: https://tools.ietf.org/html/rfc3986

**[URL]**

URL Standard. Anne van Kesteren. WHATWG. Living Standard. URL: https://url.spec.whatwg.org/

**[URN]**

URN Syntax. R. Moats. IETF. May 1997. Proposed Standard. URL: https://tools.ietf.org/html/rfc2141

**[XMLSCHEMA11-2]**

W3C XML Schema Definition Language (XSD) 1.1 Part 2: Datatypes. David Peterson; Sandy Gao; Ashok Malhotra; Michael Sperberg-McQueen; Henry Thompson; Paul V. Biron et al. W3C. 5 April 2012. W3C Recommendation. URL: https://www.w3.org/TR/xmlschema11-2/