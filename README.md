Study Font in PDF(ver 1.3)
======================

# 1. Font Data Structure
font는 PDF 내부에서 하나의 dictionary로서 표현된다. 이 dictionary는 font의 형식, Post Script 이름, 인코딩 그리고 font 프로그램이 사용하지 못할 때, 대체물에 대한 정보를 가지고 있다.
font 형식은 font dictionary안에 있는 서브형식 엔트리에 의해 구분되어진다.

|TYPE|SUBTYPE VALUE|DESCRIPTION|
|---|---|---|
|Type 0|Type0|composite font, 다른 font들로 구성된 font|
|Type 1|Type1|특별한 인코딩 포멧을 사용한 glyph로 정의한 font |
|   |MMType1|multiple master font, type 1의 확장으로 단일 font로부터 다양한 서체 스타일의 발생을 허용한다.|
|Type 3|Type3|PDF 그래픽 operator들의 스트림을 이용한 glyph로 정의된 font|
|TrueType|TrueType|TrueType font 형식의 기반한 font|
|CIDFont|CIDFontType0|Type 1 font 기술을 기반으로한 glyph description을 가진 font|
|   |CIDFontType2|TrueType font 기술을 기반으로한 glyph description을 가진 font|

font dictionary는 font의 관한 정보를 포함하는 PDF dictionary를 나타낸다. 또, CIDFont dictionary는 CIDFont에 관한 정보를 가진다. 이 dictionary는 font들의 glyph를 정의하는 font 프로그램과는 다르다. 

# 2. Simple Fonts
simple font의 몇몇의 형식들은 아래와 같은 속성들을 가지고 있다.
* 텍스트를 보여주기위한 연산자에 의해 보여지는 하나의 문자열로부터 얻은 single-byte 문자 코드를 통해 선택된다.
* 각각의 glyph는 horizontal displacement 또는 width를 포함하는 metric의 single set을 가진다.
* Type 3 font를 제외한 확실한 Type 1 font들의 모든 dictionary들은 보조 dictionary와 폰트 설명자, fontwide metrics 그리고 폰트의 다른 속성들을 가진다.

## 2.1 Type 1 Fonts
Type 1 font 프로그램은 양식화된 PostScript 프로그램이다. 이 프로그램은 glyph 모양을 설명한다. 이 것은 glyph 설명을 위해 컴팩한 인코딩을 사용한다. 그리고 작은 사이즈와 낮은 해상도에서의 고화질의 랜더링을 허용하기 위해 힌트 정보를 포함하고 있다. 

## 2.2 TrueType Fonts
TrueType font 형식은 애플 컴퓨터에서 개발되었다. 그리고 Microsoft Windows에서의 기본 폰트 형식으로 채택되었다.

TrueType font dictionary는 Type 1 font dictionay와 동일한 엔트리를 포함할 수 있다. 단 아래 사항은 유의해야한다.
* Subtype 값은 "TrueType"이다.
* BaceFont 값은 아래 쓰여진것과 다르게 파생되었다.
* 인코딩 값은 한계의 대상이다.

BaseFont의 값을 위한 PostScript 이름은 두 가지 방법 중 한 가지 방법으로 결정된다.
* PostScript name을 사용한다. 이 name은 자신의 TrueType font name 테이블 안에 있는 선택적 엔트리다.
* name 테이블안에 하나의 엔트리도 없다면, host OS내에 알려진 폰트 이름으로부터 PostScript 이름을 할당받는다 (Windows에서는 LOGFONT structrue안에 lfFaceName 필드, Mac OS에서는 FOND 리소스의 name). 만약 name이 스페이스를 포함하면 스페이스는 지워진다.

만약 source 문서의 폰트가 bold 또는 italic 스타일을 사용한다면 (그 스트일을 위한 font 데이터가 없는 것이 아니라면), host OS는 스타일을 합성할 것이다. 이러한 경우 쉼표 그리고 스타일 이름들은 font name에 붙을 것이다 ("Bold, Italic" 혹은 "BoldItalic"). 예를들어 New York font의 bold 형태는 BaseFOnt 값이 /NewYork,Bold 와 같이 쓰여질 것이다.

<pre>
17 0 obj
	<< /Type /Font
        /Subtype /TrueType
        /BaseFont /NewYork,Bold
        /FirstChar 0
        /LastChar 255
        /Widths 23 0 R
        /FontDescriptor 7 0 R
        /Encoding /MacRomanEncoding
	>>
endobj
23 0 obj
    [ 0 333 333 333 333 333 333 333 0 333 333 333 333 333 333 333 333 333
        … Omitted data …
        803 790 803 780 780 780 340 636 636 636 636 636 636 636 636 636 636
    ]
endobj
</pre>

CJK(Chinese, Japanese, Korean) font들은 host font 시스템의 "font name"을 종종 host OS script로 인코딩한다. 예를들어 하나의 일본어 font는 일본어 name을 가질텐데, 일본어 name은 일본어 인코딩을 사용한 일본어로 쓰여졌을 것이다. 게다가 TrueType font 이름들은 아마도 multiple-byte character 코드들을 포함하고 있을 것이다. 

## 2.3 Font Subsets
PDF 1.1은 Type 1과 TrueType의 서브셋을 문서들이 포함하는 것을 허용한다. font와 font 서브셋을 설명하는 font 설명자는 평병한 폰트들과는 약간 다르다. 이러한 차이는 font 서브셋을 인식하는 것을 허용하고 같은 font의 다른 서브셋을 포함한 문서들을 병합하는 것을 허용한다. font 서브셋의 경우, font의 PostScript name(font의 BaseFont 엔트리와 font 설명자의 FontName 엔트리의 값)은 뒤에 plus sign(+) 태그를 가지고 시작한다. 태그는 정확하게 6개의 대문자로 구성되어 있다. 문자의 선택은 임의로 된다. 그러나 PDF내에 다른 서브셋은 반드시 다른 태그를 가져야한다. 예를들어 EOODIA+Poetica는 Poetica®의 서브셋의 이름이다. 하나의 Type 1 font이다.

## 2.4 Character Encoding
font의 인코딩은 (보여진 텍스트 문자열로부터 얻어진)character code들과 glyph description 사이의 연관(연합?)이다.
Type 3 font들을 제외하고 모든 font 프로그램은 내장된 인코딩을 갖는다. 특정한 상황에서, PDF dont dictionary는 애플리케이션의 요구에 맞게 보여지는 텍스트를 생성하기 위해 내장된 font 인코딩을 변경할 수 있다. 이러한 character 인코딩의 유연성은 두가지 측면에서 가치가 있다. 
* 이것은 다양한 convention을 따라 인코딩된 텍스트들을 보여주는 것을 허용한다. 예를들어, Microsoft Windows와 Apple Mac OS OS에서는 Latin 텍스트를 보여주기 위해 서로 다른 기본 인코딩을 사용하고 있고, 많은 application은 자신의 특별한 목적 인코딩을 사용하고 있다.
* 이것은 application이 인코딩된 large character set으로부터 character들을 어떻게 선택하는지에 대한 것을 명시하는 것을 허용한다. 몇몇의 character set들은 256개의 문자보다 더 많은 문자로 구성되어 있다. 이것에는 ligature(합자?)와 accent 부호 그리고 다른 기호들(고품질 인쇄나 non-Lartin 쓰기를 위한 시스템을 위해을 포함한다. 다른 인코딩들은 같은 character set의 다른 서브셋을 선택할 수 있다.

...306 페이지 이후 생략...

### 2.4.1 TrueType Fonts를 위한 인코딩
TrueType font 프로그램의 내장 인코딩은 "cmap"이라고 불리는 내부 데이터 구조를 사용하여 직접적으로 character code에서 glyph description으로 매핑되어 있다 (CMap과 헷갈리지 말 것). TrueType font 프로그램은 multiple 인코딩을 포함할 수 있다. multiful 인코딩은 서로 다른 플랫폼(Mac OS or WIndows)에서 사용할 수 있다는 것을 의미한다. PDF font dictionaray의 인코딩 엔트리는 이용가능한 인코딩을 선택할 수 있다. 만약에 이 엔트리의 이용가능한 엔트리가 없다면 구현-의존적인 인코딩을 선택한다.

TrueType에서는 이름지어진 character들을 위한 기본적인 지원이 없지만 몇몇의 font 프로그램들은 glyph들은 위한 character name들을 열거한 선택적인 "post" 테이블을 가지고 있다. 만약 뷰어 애플리케이션이 "post" 테이블이 없는 font 프로그램에서 name으로 glyph description을 선택하는 것이 필요하다면, 이것은 charcter name들을 font 프로그램의 "cmap" 테이블내에 주어진 인코딩중의 하나를 이용하여 code로 바꾸는 것이 필요하다.

TrueType glyph 선택된 모양은 뷰어의 수행 또는 OS의 의존적이기 때문에, TrueType 폰트들을 사용하는 PDF File들은 모든 뷰어 애플리케이션들의 실행이 예측가능한 것을 보장하기 위해 확실한 가이드라인들 따라야한다. font 프로그램은 내장되어 있어야한다. nonsymbolic font는 그 것의 인코딩 엔트리에 MacRomanEncoding인지 WinAnsiEncoding인지를 명시해야한다. symbolic font는 인코딩 엔트리를 명시하지 않아도 된다. font 프로그램 "cmap" table은 정확한 하나의 인코딩을 포함하고 있어야한다. "cmap" 테이블의 내용에 대한 가이드라인을 아래에 명시했다.

Note: 몇몇의 유명한 TrueType font 프로그램들은 정확하지 않은 인코딩 정보를 포함하고 있다. TrueType font interpreter들의 구현은 이런 문제들을 다루기위한 휴리스틱(heuristics)을 발전시켰다. 이러한 휴리스틱들은 여기서 설명하지 않는다. 최대의 이식성을 위해 오직 잘 정리된 TrueType font 프로그램만 PDF File내에 사용해야한다.

이어지는 단락들은 PDF 1.4에서 TrueType font 인코딩을 다루는 것을 설명한다. 최신 버전에서는 필요하지 않은 적용 방법에 대한 설명이다.
 TrueType font 프로그램의 "cmap" 테이블은 하나 이상의 서브테이블로 구성되어있다. 각각은 Platform ID와 platform-specific encoding ID의 조합으로 구성되어 있다. 만약 인코딩의 name (WinAnsiEncoding, MacRomanEncoding 또는 MacExpertEncoding)이 font dictionary의 인코딩 엔트리 또는 인코딩 dictionary의 BaseEndoding 엔트리내에 명시되어 있다면, "cmap" 서브테이블은 선택되고 사용된다. 이에 대한 설명은 아래에 있다.
 * 만약 platform ID 3과 encoding ID 1(Microsoft Unicode)을 가진 "cmap" 서브테이블이 나타났다면, 이것은 다음과 같이 사용된다. 먼저 Chracter code는 font의 인코딩 엔트리에 의해 명시된 character name에 맵핑된다. 그후 character name은 Adobe Glyph List를 참조하여 Unicode 값으로 맵핑된다. 마지막으로 Unicode 값은 (3, 1) 서브테이블에 따라 glyph description을 매핑한다.
* 만약 platform ID 1과 encoding ID 0(Macintosh Roman)을 가진 "cmap" 서브테이블이 나타났다면, 이것은 다음과 같이 사용된다. 먼저 Chracter code는 font의 인코딩 엔트리에 의해 명시된 character name에 맵핑된다. 그후 character name은 MacRomanEncoding을 참조하여 Unicode 값으로 맵핑된다. 마지막으로 Unicode 값은 (1, 0) 서브테이블에 따라 glyph description을 매핑한다.
* 위에 경우중 하나에서, 만약 character name을 명시된대로 맵핑할 수 없는 경우, character name은 font 프로그램의 "post" 테이블에서 찾아지고 연관된 glyph description이 사용되어진다.

 <pre> Character Code -> Character Name -> Unicode Value -> Glyph Description </pre>

font dictionary안에 명시된 인코딩 엔트리가 없을 경우에는 character name에 대한 고려 없이 platform ID 1과 encoding ID 0을 가진"cmap" 서브 테이블이 character code로부터 glyph description을 직접 매핑하는 데 사용되어진다. 이것은 symbolic font에 대한 일반적인 convetion이다.

만약 위에 언급한 어떠한 방법으로도 character가 맵핑될 수 없다면 그 결과는 구현-의존적일 것이다.



