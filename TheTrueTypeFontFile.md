Reference: Apple, TrueType Reference Manual, https://developer.apple.com/fonts/TrueType-Reference-Manual/RM06/Chap6.html

# The TrueType Font File
## Introduction
이번 챕터에서는 TrueType 폰트 파일을 구성하는 테이블을 설명한다. 이 내용에는 핵심 TrueType 명세에 AAT 확장들을 포함한다. 문서는 OpenType-specific 테이블을 포함하지는 않는다. 이것들은 OS X와 iOS에서만 지원한다. 이러한 테이블에 대한 정보를 보기위해서는 OpenType specification을 참고하라.

Table 1은 모든 platform에서 사용하는 TrueType font 파일에서 사용하는 데이터 타입을 설명한다.

## Data Types
기본 integer 데이터 타입 이외에, TrueType 폰트 형식은 아래와 같은 데이터 타입을 사용한다.

Table 1: 'sfnt' 데이터 타입

|Data Type|Description|
|:---|:---|
|shortFrac|부호를 가진 16비트 분수|
|Fixed|부호를 가진 16비트 고정소수점 실수|
|FWord|부호를 가진 16비트 정수. FUnit. em 공간에서의 최소 측정 단위|
|uFWord|양의 부호만 가진 16비트 정수. FUnit. em 공간에서의 최소 측정 단위|
|F2Dot14|하위 14비트를 분수로 표현하는 부호를 가진 16비트 고정소수점 실수|
|longDateTime|date의 긴 내부 형식으로 부호를 가진 64비트 정수로 표현된다|

## TrueType Font Files: an overview
TrueType 폰트 파일은 연결된 테이블의 연속으로 구성되어 있다. 하나의 테이블은 word의 연속이다. 각각의 테이블은 반드시 길게 정렬되어있어야하고 필요하다면 0으로 채워져있어야한다.

첫번째 테이블은 폰트 디렉토리이다. 이 특별한 테이블은 폰트 내에 다른 테이블과의 접근을 수월하게 한다. 이 디렉토리 다음에는 폰트 데이터를 포함하고 있는 순서화된 테이블이 온다. 이러한 테이블들은 어떠한 순서로든 나타날 수 있다. 모든 폰트에는 확실하게 테이블이 필요하다. 다른 것들은 특정 폰트의 기능에 따라 선택적이다.

테이블들은 알려진 태그로서 이름을 갖고 있다. 태그는 uint32 타입이다. 현재는 태그 이름들은 4개의 문자로 구성되어 있다. 4개의 문자보다 작은 문자로 구성된 태그 이름들은 공백을 채워 4자리로 표현한다. 태그 이름을 텍스트로서 보여줄 때는 작은 따음표로 감싸서 표현한다.

Table 2: The required tables

|Tag|Table|
|:---|:---|
|'cmap'|character to glyph mapping|
|'glyf'|glyph data|
|'head'|font header|
|'hhea'|horizontal header|
|'hmtx'|horizontal matrics|
|'loca'|index to location|
|'maxp'|maximum profile|
|'name'|naming|
|'post'|PostScript|

Table 2에 나열된 "required" 테이블은 오직 TrueType 폰트에서만 요구된다. 다른 다양한 sfnt-housed 폰드는 이러한 테이블을 가지지 않는다.

Table 3: The optional tables

|Tag|Table|
|:---|:---|
|'cvt'|control value|
|'fpgm'|font program|
|'hdmx'|horizontal device metrics|
|'kern'|kerning|
|'OS/2'|OS/2|
|'prep'|control value program|

## The Font Directory
테이블의 첫번째는 폰트 디렉토리는 폰트 파일의 내용을 안내한다. 이것은 다른 테이블에 있는 데이터에 접근할 수 있는 정보를 제공한다. 이 디렉토리는 두 가지 부분으로 구성되어 있다. offset 서브테이블과 테이블 디렉토리이다. offset 서브테이블은 폰트 내에 테이블의 갯수를 기록하고 디렉토리 테이블에 빠르게 접근할 수 있도록 offset 정보를 제공한다. 테이블 디렉토리는 폰트 내에 테이블 항목의 연속이다.

### The offset subtable
offset 서브테이블은, Table 4에 설명되어 있다. offset 서브테이블은 폰트의 scaler 형식으로 시작한다. 'sfnt;에 태그된 테이블의 수는 다음과 같다. 테이블 디렉토리와 어떤 서브테이블들은 이 숫자를 포함하고 있지 않는다. searchRange, entrySelector, rangeShift를 위한 항목들은 테이블 디렉토리를 빠르게 이진 검색하는 데에 유용하게 사용되곤 한다. 폰트가 많은 테이블들 가지고 있지 않다면, 연속 검색은 충분히 빠를 것이다.

(... 대충 이진 검색이 좋다는 내용이 주를 이루기 때문에 생략한다 ... )

Table 4: The offset subtable

|Type|Name|Description|
|:---|:---|:---|
|uint32|scaler type|A tag to indicate the OFA scaler to be used to rasterize this font; see the note on the scaler type below for more information.|
|uint16|numTables|number of tables|
|uint16|searchRange|(maximum power of 2 <= numTables)*16|
|uint16|entrySelector|log2(maximum power of 2 <= numTables)|
|uint16|rangeShift|	numTables*16-searchRange|

### The table directory
테이블 디렉토리는 offset 서브테이블을 뒤에 온다. 테이블 디렉토리내에 항목들은 반드시 태그 순서로 오름차순으로 정렬되어야한다. 각각의 폰트 파일내에 테이블은 자신의 테이블 디렉토리 항목을 가져야한다. Table 5는 테이블 디렉토리의 구조를 설명한다.

Table 5: The table directory

|Type|Name|Description|
|:---|:---|:---|
|uint32|tag|4-byte identifier|
|uint32|checkSum|checksum for this table|
|uint32|offset|offset from beginning of sfnt|
|uint32|length|length of this table in byte (actual length not padded length)|

테이블 디렉토리는 chaeckSum을 가지고 있다. 이 숫자는 자신과 연결된 태그된 테이블에서 테이터가 일치하는 지 무결한지 확인하는데 사용된다. 다음에 오는 C function은 주어진 테이블의 checkSum을 결정하는데 사용되는 함수이다.

<pre>
uint32 CalcTableChecksum(uint32 *table, uint32 numberOfBytesInTable)
    {
    uint32 sum = 0;
    uint32 nLongs = (numberOfBytesInTable + 3) / 4;
    while (nLongs-- > 0)
        sum += *table++;
    return sum;
    }
</pre>






