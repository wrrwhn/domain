+++
date = "2016-12-16T22:07:46+08:00"
title = "XSD"
draft = false
tags = ["整理","XSD"]
share = true
+++


## 简介
- 全称为 XML Schema 定义，即 XML Schema Definition (XSD)
- XML Schema 是基于 XML 的 DTD 替代，用于描述 XML 文档的结构。
- 支持数据类型，便于描述、验证正确性和约束、转换、定义等工作。

## 参考
- 教程：http://www.w3school.com.cn/schema/
- 手册：http://www.w3school.com.cn/schema/schema_elements_ref.asp
- 验证：http://www.ltg.ed.ac.uk/~ht/xsv-status.html
- 实例：http://www.w3school.com.cn/schema/schema_example.asp
## 使用
### XML结构
```
<?xml version="1.0"?>
    <note>
    <to>George</to>
    <from>John</from>
    <heading>Reminder</heading>
    <body>Don't forget the meeting!</body>
</note>
```

### 定义 - note.xsd
```
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"        // 指定该 Schema 中的元素和数据类型的命名空间来源， 同时指定当前命名空间的元素和类型都应需补充前缀 xs:
    targetNamespace="http://www.w3school.com.cn"            // 显示定义元素(node /to /from /heading /body)的来源命名空间
    xmlns="http://www.w3school.com.cn"                        // 指定默认命名空间
    elementFormDefault="qualified"
>
    <xs:element name="note">
        <xs:complexType>
          <xs:sequence>
        <xs:element name="to" type="xs:string"/>
        <xs:element name="from" type="xs:string"/>
        <xs:element name="heading" type="xs:string"/>
        <xs:element name="body" type="xs:string"/>
          </xs:sequence>
        </xs:complexType>
    </xs:element>
</xs:schema>
```

### 引用
```
<?xml version="1.0"?>
<note
    xmlns="http://www.w3school.com.cn"                                // 默认命名空间的声明
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"            // 指定 XML Schema 实例命名空间
    xsi:schemaLocation="http://www.w3school.com.cn note.xsd"            // 指定实例命名空间后使用， 用于指定需要使用的命名空间和供命名空间使用的 XML Schema 位置
>
    <to>George</to>
    <from>John</from>
    <heading>Reminder</heading>
    <body>Don't forget the meeting!</body>
</note>
```

## 类型
### 简单类型

#### 元素
```
<xs:element name="xxx" type="yyy"/>
<xs:element name="color" type="xs:string" default="red"/>        // 默认值
<xs:element name="color" type="xs:string" fixed="red"/>            // 固定值
<xs:attribute name="lang" type="xs:string" use="required"/>        // 缺省情况下可选

type 可选值
    xs:string
    xs:decimal
    xs:integer
    xs:boolean
    xs:date
    xs:time
```

#### 属性
- `<lastname lang="EN">Smith</lastname>`

#### 限定
- 值范围
```
age 限定为 0 到 120 的整数值
    <xs:element name="age">
        <xs:simpleType>
          <xs:restriction base="xs:integer">
            <xs:minInclusive value="0"/>
            <xs:maxInclusive value="120"/>
          </xs:restriction>
        </xs:simpleType>
    </xs:element>
letter 属性只接受小写字母  a - z 中零个或多个字母
    <xs:element name="letter">
        <xs:simpleType>
          <xs:restriction base="xs:string">
            <xs:pattern value="([a-z])*"/>        // value 可使用正则相关约定
          </xs:restriction>
        </xs:simpleType>
    </xs:element>
address 属性中的所有空白字符（换行、回车、空格以及制表符会被替换为空格，开头和结尾的空格会被移除，而多个连续的空格会被缩减为一个单一的空格）将被移除
    <xs:element name="address">
        <xs:simpleType>
          <xs:restriction base="xs:string">
            <xs:whiteSpace value="collapse"/>   
                // value== preserve 时将不进行处理
                // value== replace 时将移除所有空白字符
          </xs:restriction>
        </xs:simpleType>
    </xs:element>
```
- 枚举值
```
限定类型名称只可选 Audi Golf 和 BMW
    写法一：
        <xs:element name="car">
            <xs:simpleType>
              <xs:restriction base="xs:string">
                <xs:enumeration value="Audi"/>
                <xs:enumeration value="Golf"/>
                <xs:enumeration value="BMW"/>
              </xs:restriction>
            </xs:simpleType>
        </xs:element>
    写法二：
        <xs:element name="car" type="carType"/>
        <xs:simpleType name="carType">
          <xs:restriction base="xs:string">
            <xs:enumeration value="Audi"/>
            <xs:enumeration value="Golf"/>
            <xs:enumeration value="BMW"/>
          </xs:restriction>
        </xs:simpleType>
    ```

- 长度限定
```
password 属性长度必须为 8 位
    <xs:element name="password">
        <xs:simpleType>
          <xs:restriction base="xs:string">
            <xs:length value="8"/>
          </xs:restriction>
        </xs:simpleType>
    </xs:element>
password 属性长度可为 5 到 8 位
<xs:element name="password">
    <xs:simpleType>
      <xs:restriction base="xs:string">
        <xs:minLength value="5"/>
        <xs:maxLength value="8"/>
      </xs:restriction>
    </xs:simpleType>
</xs:element>
```

#### 扩展(数据类型限定)
```
    限定            描述
    enumeration        定义可接受值的一个列表
    fractionDigits    定义所允许的最大的小数位数。必须大于等于0。
    length            定义所允许的字符或者列表项目的精确数目。必须大于或等于0。
    maxExclusive    定义数值的上限。所允许的值必须小于此值。
    maxInclusive    定义数值的上限。所允许的值必须小于或等于此值。
    maxLength        定义所允许的字符或者列表项目的最大数目。必须大于或等于0。
    minExclusive    定义数值的下限。所允许的值必需大于此值。
    minInclusive    定义数值的下限。所允许的值必需大于或等于此值。
    minLength        定义所允许的字符或者列表项目的最小数目。必须大于或等于0。
    pattern            定义可接受的字符的精确序列。
    totalDigits        定义所允许的阿拉伯数字的精确位数。必须大于0。
    whiteSpace        定义空白字符（换行、回车、空格以及制表符）的处理方式。
```

### 复合类型

#### 空元素
- XML 样式
    - `<product pid="1345"/>`
- XSD 写法
```
<xs:complexType name="prodtype">
  <xs:attribute name="pid" type="xs:positiveInteger"/>
</xs:complexType>
    或
<xs:complexType>
  <xs:complexContent>
    <xs:restriction base="xs:integer">
      <xs:attribute name="pid" type="xs:positiveInteger"/>
    </xs:restriction>
  </xs:complexContent>
</xs:complexType>
```

#### 复合元素
- XML 样式
```
<employee>
    <firstname>John</firstname>
    <lastname>Smith</lastname>
</employee>
```
- XSD 写法
```
<xs:complexType name="persontype">
  <xs:sequence>                                            // 限定元素依序出现
    <xs:element name="firstname" type="xs:string"/>
    <xs:element name="lastname" type="xs:string"/>
  </xs:sequence>
</xs:complexType>
```

#### 仅包含文本的复合元素
- XML 样式
    - `<shoesize country="france">35</shoesize>`
- XSD 写法
```
<xs:element name="shoesize">
  <xs:complexType>
    <xs:simpleContent>                                        // 仅包含简单内容（文本和属性），因此应该使用 xs:simpleContent 标签
      <xs:extension base="xs:integer">                            // 填充内容类型
        <xs:attribute name="country" type="xs:string" />            // 属性名称和其属性
      </xs:extension>
    </xs:simpleContent>
  </xs:complexType>
</xs:element>
```

#### 包含元素和文本的元素
- XML 样式
```
<letter>
    Dear Mr.<name>John Smith</name>.
    Your order <orderid>1032</orderid>
    will be shipped on <shipdate>2001-07-13</shipdate>.
</letter>
```
- XSD 写法
```
<xs:element name="letter">
  <xs:complexType mixed="true">                                    // 标志字符数据可出现在 Letter 的子元素之间
    <xs:sequence>
      <xs:element name="name" type="xs:string"/>
      <xs:element name="orderid" type="xs:positiveInteger"/>
      <xs:element name="shipdate" type="xs:date"/>
    </xs:sequence>
  </xs:complexType>
</xs:element>
```

#### 写法
- 纯声明
```
<xs:element name="employee">
  <xs:complexType>
    <xs:sequence>
      <xs:element name="firstname" type="xs:string"/>
      <xs:element name="lastname" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>
</xs:element>
```

- 重复引用
```
<xs:element name="employee" type="personinfo"/>
<xs:element name="student" type="personinfo"/>
<xs:element name="member" type="personinfo"/>
<xs:complexType name="personinfo">
  <xs:sequence>
    <xs:element name="firstname" type="xs:string"/>
    <xs:element name="lastname" type="xs:string"/>
  </xs:sequence>
</xs:complexType>
```
- 复合引用
```
    <xs:element name="employee" type="fullpersoninfo"/>
    <xs:complexType name="personinfo">
      <xs:sequence>
        <xs:element name="firstname" type="xs:string"/>
        <xs:element name="lastname" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
    <xs:complexType name="fullpersoninfo">
      <xs:complexContent>
        <xs:extension base="personinfo">
          <xs:sequence>
            <xs:element name="address" type="xs:string"/>
            <xs:element name="city" type="xs:string"/>
            <xs:element name="country" type="xs:string"/>
          </xs:sequence>
        </xs:extension>
      </xs:complexContent>
    </xs:complexType>
```

#### 指示器
- 概念
    - 用于控制文档中元素使用的方式
- 类型
    - Order 指示器：定义元素顺序
        - All：规定子元素可按任意顺序出来，且每个子元素必须也只出现一次
        - Choice：下属只元素只能出现一个
        - Sequence：必须按照特定顺序出现
    - Occurrence 指示器：定义某元素的出现频率
        - 注：对于所有的 "Order" 和 "Group" 指示器，其中的 maxOccurs 以及 minOccurs 的默认值均为 1。
        - maxOccurs：规定某个元素可出现的最大次数，其中若值为 unbounded 则不限制次数
        - minOccurs：规定某个元素能够出现的最小次数
    - Group 指示器：
        - Group name ：元素组，内部必须定义一个 all、choice 或者 sequence 元素
        - attributeGroup name ：属性组
- 示例
```
<xs:element name="person">
  <xs:complexType>
    <xs:all>
      <xs:element name="firstname" type="xs:string"/>
      <xs:element name="lastname" type="xs:string"/>
    </xs:all>
  </xs:complexType>
</xs:element>
<xs:element name="person">
  <xs:complexType>
    <xs:choice>
      <xs:element name="employee" type="employee"/>
      <xs:element name="member" type="member"/>
    </xs:choice>
  </xs:complexType>
</xs:element>
<xs:element name="person">
  <xs:complexType>
    <xs:sequence>
      <xs:element name="firstname" type="xs:string"/>
      <xs:element name="lastname" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>
</xs:element>
<xs:element name="person">
  <xs:complexType>
    <xs:sequence>
      <xs:element name="full_name" type="xs:string"/>
      <xs:element name="child_name" type="xs:string" maxOccurs="unbounded"/>            // 不限制出现次数
    </xs:sequence>
  </xs:complexType>
</xs:element>
<xs:element name="person">
  <xs:complexType>
    <xs:sequence>
      <xs:element name="full_name" type="xs:string"/>
      <xs:element name="child_name" type="xs:string" maxOccurs="10" minOccurs="0"/>        // child_name最少出现0次，最多出现10次
    </xs:sequence>
  </xs:complexType>
</xs:element>
<xs:element name="person" type="personinfo"/>
<xs:complexType name="personinfo">
  <xs:sequence>
    <xs:group ref="persongroup"/>
    <xs:element name="country" type="xs:string"/>
  </xs:sequence>
</xs:complexType>
<xs:group name="persongroup">
  <xs:sequence>
    <xs:element name="firstname" type="xs:string"/>
    <xs:element name="lastname" type="xs:string"/>
    <xs:element name="birthday" type="xs:date"/>
  </xs:sequence>
</xs:group>
<xs:element name="person">
  <xs:complexType>
    <xs:attributeGroup ref="personattrgroup"/>
  </xs:complexType>
</xs:element>
<xs:attributeGroup name="personattrgroup">
  <xs:attribute name="firstname" type="xs:string"/>
  <xs:attribute name="lastname" type="xs:string"/>
  <xs:attribute name="birthday" type="xs:date"/>
</xs:attributeGroup>
```

#### 扩展
- 定义
    - 通过未被 Schema 规定的元素来拓展 XML 文本
- Any(任意元素)
    - 初始定义
    ```
    <xs:element name="person">
      <xs:complexType>
        <xs:sequence>
          <xs:element name="firstname" type="xs:string"/>
          <xs:element name="lastname" type="xs:string"/>
          <xs:any minOccurs="0"/>                                // 预留扩展位置
        </xs:sequence>
      </xs:complexType>
    </xs:element>
    ```

    - 拓展元素
    ```
    <xs:element name="children">                                // 拓展属性
      <xs:complexType>
        <xs:sequence>
          <xs:element name="childname" type="xs:string"
          maxOccurs="unbounded"/>
        </xs:sequence>
      </xs:complexType>
    </xs:element>
    ```

    - XSD 引用
    ```
    <persons xmlns="http://www.microsoft.com"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:SchemaLocation="http://www.microsoft.com family.xsd
        http://www.w3school.com.cn children.xsd"
    >
    <person>
        <firstname>David</firstname>
        <lastname>Smith</lastname>
        <children>
          <childname>mike</childname>
        </children>
    </person>
    ```

- AnyAttribute(任意属性)
    - 初始定义
    ```
    <xs:element name="person">
      <xs:complexType>
        <xs:sequence>
          <xs:element name="firstname" type="xs:string"/>
          <xs:element name="lastname" type="xs:string"/>
        </xs:sequence>
        <xs:anyAttribute/>
      </xs:complexType>
    </xs:element>
    ```
    - 拓展元素
    ```
    <xs:attribute name="gender">
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:pattern value="male|female"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    ```
    - XSD 引用
    ```
    <person gender="female">
        <firstname>Jane</firstname>
        <lastname>Smith</lastname>
    </person>
    ```

#### 元素替换
- 定义
    - 进行元素之间的替换，如多语言定义，针对不同国家允许其使用本国语言进行相关的 XML 格式定义
- 示例
    - XSD 定义
    ```
    <xs:element name="name" type="xs:string"/>
    <xs:element name="navn" substitutionGroup="name"/>
    <xs:element name="customer" type="custinfo"/>
    <xs:element name="kunde" substitutionGroup="customer"/>
    <xs:complexType name="custinfo">
      <xs:sequence>
        <xs:element ref="name"/>
      </xs:sequence>
    </xs:complexType>
    ```
    - 有效 XML
    ```
    <customer>
      <name>John Smith</name>
    </customer>
    <kunde>
      <navn>John Smith</navn>
    </kunde>
    ```
- 注
    - <xs:element name="name" type="xs:string" block="substitution"/>        // 防止其它元素替换指定元素
    - substitutionGroup 作用的元素，都必须声明为全局元素
        - 全局元素： Schema 层级下的直接子元素


## 解析实例
### XML 数据
```
<?xml version="1.0" encoding="ISO-8859-1"?>
<shiporder orderid="889923" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="shiporder.xsd">
  <orderperson>George Bush</orderperson>
  <shipto>
    <name>John Adams</name>
    <address>Oxford Street</address>
    <city>London</city>
    <country>UK</country>
  </shipto>
  <item>
    <title>Empire Burlesque</title>
    <note>Special Edition</note>
    <quantity>1</quantity>
    <price>10.90</price>
  </item>
  <item>
    <title>Hide your heart</title>
    <quantity>1</quantity>
    <price>9.90</price>
  </item>
</shiporder>
```

### XSD 格式
- 写法一
```
<?xml version="1.0" encoding="ISO-8859-1" ?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="shiporder">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="orderperson" type="xs:string"/>
        <xs:element name="shipto">
          <xs:complexType>
            <xs:sequence>
              <xs:element name="name" type="xs:string"/>
              <xs:element name="address" type="xs:string"/>
              <xs:element name="city" type="xs:string"/>
              <xs:element name="country" type="xs:string"/>
            </xs:sequence>
          </xs:complexType>
        </xs:element>
        <xs:element maxOccurs="unbounded" name="item">
          <xs:complexType>
            <xs:sequence>
              <xs:element name="title" type="xs:string"/>
              <xs:element minOccurs="0" name="note" type="xs:string"/>
              <xs:element name="quantity" type="xs:positiveInteger"/>
              <xs:element name="price" type="xs:decimal"/>
            </xs:sequence>
          </xs:complexType>
        </xs:element>
      </xs:sequence>
      <xs:attribute name="orderid" type="xs:string" use="required"/>
    </xs:complexType>
  </xs:element>
</xs:schema>
```
- 写法二
```
<?xml version="1.0" encoding="ISO-8859-1" ?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <!-- 简易元素的定义 -->
  <xs:element name="orderperson" type="xs:string"/>
  <xs:element name="name" type="xs:string"/>
  <xs:element name="address" type="xs:string"/>
  <xs:element name="city" type="xs:string"/>
  <xs:element name="country" type="xs:string"/>
  <xs:element name="title" type="xs:string"/>
  <xs:element name="note" type="xs:string"/>
  <xs:element name="quantity" type="xs:positiveInteger"/>
  <xs:element name="price" type="xs:decimal"/>
  <!-- 属性的定义 -->
  <xs:attribute name="orderid" type="xs:string"/>
  <!-- 复合元素的定义-->
  <xs:element name="shipto">
    <xs:complexType>
      <xs:sequence>
        <xs:element ref="name"/>
        <xs:element ref="address"/>
        <xs:element ref="city"/>
        <xs:element ref="country"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
  <xs:element name="item">
    <xs:complexType>n
      <xs:sequence>
        <xs:element ref="title"/>
        <xs:element minOccurs="0" ref="note"/>
        <xs:element ref="quantity"/>
        <xs:element ref="price"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
  <xs:element name="shiporder">
    <xs:complexType>
      <xs:sequence>
        <xs:element ref="orderperson"/>
        <xs:element ref="shipto"/>
        <xs:element maxOccurs="unbounded" ref="item"/>
      </xs:sequence>
      <xs:attribute ref="orderid" use="required"/>
    </xs:complexType>
  </xs:element>
</xs:schema>
```
- 写法三
```
<?xml version="1.0" encoding="ISO-8859-1" ?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:simpleType name="stringtype">
    <xs:restriction base="xs:string"/>
  </xs:simpleType>
  <xs:simpleType name="inttype">
    <xs:restriction base="xs:positiveInteger"/>
  </xs:simpleType>
  <xs:simpleType name="dectype">
    <xs:restriction base="xs:decimal"/>
  </xs:simpleType>
  <xs:simpleType name="orderidtype">
    <xs:restriction base="xs:string">
      <xs:pattern value="[0-9]{6}"/>
    </xs:restriction>
  </xs:simpleType>
  <xs:complexType name="shiptotype">
    <xs:sequence>
      <xs:element name="name" type="stringtype"/>
      <xs:element name="address" type="stringtype"/>
      <xs:element name="city" type="stringtype"/>
      <xs:element name="country" type="stringtype"/>
    </xs:sequence>
  </xs:complexType>
  <xs:complexType name="itemtype">
    <xs:sequence>
      <xs:element name="title" type="stringtype"/>
      <xs:element minOccurs="0" name="note" type="stringtype"/>
      <xs:element name="quantity" type="inttype"/>
      <xs:element name="price" type="dectype"/>
    </xs:sequence>
  </xs:complexType>
  <xs:complexType name="shipordertype">
    <xs:sequence>
      <xs:element name="orderperson" type="stringtype"/>
      <xs:element name="shipto" type="shiptotype"/>
      <xs:element maxOccurs="unbounded" name="item" type="itemtype"/>
    </xs:sequence>
    <xs:attribute name="orderid" type="orderidtype" use="required"/>
  </xs:complexType>
  <xs:element name="shiporder" type="shipordertype"/>
</xs:schema>
```

## 数据类型
### 字符串
- 可选类型
    - xs:string：可包含字符、换行、回车以及制表符。
    - xs:normalizedString：可包含字符，但是 XML 处理器会移除折行，回车以及制表符。
    - xs:token：可包含字符，但是 XML 处理器会移除换行符、回车、制表符、开头和结尾的空格以及（连续的）空格。
- 支持限定
    - enumeration
    - length
    - maxLength
    - minLength
    - pattern (NMTOKENS、IDREFS 以及 ENTITIES 无法使用此约束)
    - whiteSpace

### 日期
- 可选类型
    - xs:date：时间格式为 YYYY-MM-DD
    - xs:time：时间格式为 hh:mm:ss
    - xs:dateTime：时间格式为 YYYY-MM-DDThh:mm:ss，限定输入应为 2002-05-30T09:00:00[.5]
    - xs:duration：用于规定时间间隔，时间格式为 PnYnMnDTnHnMnS
        - P 表示周期(必需)
        - nY 表示年数
        - nM 表示月数
        - nD 表示天数
        - T 表示时间部分的起始 （如果您打算规定小时、分钟和秒，则此选项为必需）
        - nH 表示小时数
        - nM 表示分钟数
        - nS 表示秒数
    - 示例：
        - P5Y2M10DT15H 表示周期为 5年2个月10天及15小时
- 支持限定
    - enumeration
    - maxExclusive
    - maxInclusive
    - minExclusive
    - minInclusive
    - pattern
    - whiteSpace

###数值
- 可选类型
    - xs:decimal：十进制数据，最大位数为 18 位
    - xs:integer：整数
- 支持限定
    - enumeration
    - fractionDigits
    - maxExclusive
    - maxInclusive
    - minExclusive
    - minInclusive
    - pattern
    - totalDigits
    - whiteSpace

### 杂项
- 类型
    - 布尔值
        - xs:boolean：逻辑项，仅可输入 true | false
    - 二进制
        - xs:hexBinary：十六进制编码的二进制数据
        - xs:base64Binary：Base64 编码的二进制数据
    - URI
        - xs:anyURI：用于限定输入值仅可为 URL，且若含有空格，需用 %20 替换。
- 支持限定
    - enumeration (布尔数据类型无法使用此约束*)
    - length (布尔数据类型无法使用此约束)
    - maxLength (布尔数据类型无法使用此约束)
    - minLength (布尔数据类型无法使用此约束)
    - pattern
    - whiteSpace
