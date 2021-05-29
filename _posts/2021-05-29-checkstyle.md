## checkstyle 使用

### 在gradle中配置checkstyle
```gradle
    apply plugin: 'checkstyle'

    checkstyle {
            toolVersion = "8.38"
            ignoreFailures = false
            showViolations = true
        }

        task Checkstyle(type: Checkstyle) {
            source = 'src/main/java'
            include '**/*.java'
            exclude '**/gen/**'
            exclude '**/R.java'
            exclude '**/BuildConfig.java'

            classpath = files()
        }
```

### checkstyle.xml
默认路径 /config/checkstyle/checkstyle.xml

google 的checkstyle.xml，做了一些更改，尽量与IDEA保持一致，减少配置成本

#### 主要更改
- 每行最大字符数（与IDEA保持一致）
```xml
    <!--检查行长度-->
    <module name="LineLength">
        <property name="fileExtensions" value="java"/>
        <property name="max" value="120"/>
        <property name="ignorePattern" value="^package.*|^import.*|a href|href|http://|https://|ftp://"/>
    </module>
```
- 缩进，4个字符（换行策略与IDEA保持一致）
```xml
    <!--检查Java代码的正确缩进-->
    <module name="Indentation">
        <!--换行默认缩进字符数-->
        <property name="basicOffset" value="4" /><!--mine-->
        <!--在下一行时，大括号应缩进多远-->
        <property name="braceAdjustment" value="0" />
        <property name="caseIndent" value="4" /><!--mine-->
        <property name="throwsIndent" value="4" />
        <property name="lineWrappingIndentation" value="8" />
        <property name="arrayInitIndent" value="4" /><!--mine-->
    </module>
```
- 不检查连续大写字母的长度，AABBcc是可被接受的
```xml
    <!--<module name="AbbreviationAsWordInName">-->
```
- import顺序，第三方 -> javax|java -> static（与IDEA保持一致）
 ```xml
 <!--检查导入声明组是否按用户指定的顺序显示-->
        <module name="CustomImportOrder">
            <property name="customImportOrderRules"
                      value="THIRD_PARTY_PACKAGE###SPECIAL_IMPORTS###STANDARD_JAVA_PACKAGE###STATIC"/>
            <property name="specialImportsRegExp" value="^javax\."/>
            <property name="standardPackageRegExp" value="^java\."/>
            <!--是否按字母顺序导包-->
            <property name="sortImportsInGroupAlphabetically" value="true"/>
            <property name="separateLineBetweenGroups" value="false"/>
        </module>
 ```
 - 暂不考虑Javadoc
 - 


#### 全部设置
```xml
<?xml version="1.0"?>
<!DOCTYPE module PUBLIC
        "-//Checkstyle//DTD Checkstyle Configuration 1.3//EN"
        "https://checkstyle.org/dtds/configuration_1_3.dtd">

<!--
    Checkstyle configuration that checks the Google coding conventions from Google Java Style
    that can be found at https://google.github.io/styleguide/javaguide.html

    Checkstyle is very configurable. Be sure to read the documentation at
    http://checkstyle.org (or in your downloaded distribution).

    To completely disable a check, just comment it out or delete it from the file.
    To suppress certain violations please review suppression filters.

    Authors: Max Vetrenko, Ruslan Diachenko, Roman Ivanov.
 -->

<module name = "Checker">
    <!--字符集编码-->
    <property name="charset" value="UTF-8"/>
    <!--违规级别-->
    <property name="severity" value="warning"/>
    <!--可接受的文件扩展名-->
    <property name="fileExtensions" value="java, properties, xml"/>
    <!-- Excludes all 'module-info.java' files              -->
    <!-- See https://checkstyle.org/config_filefilters.html -->
    <module name="BeforeExecutionExclusionFileFilter">
        <property name="fileNamePattern" value="module\-info\.java$"/>
    </module>
    <!-- https://checkstyle.org/config_filters.html#SuppressionFilter -->
    <module name="SuppressionFilter">
        <property name="file" value="${config_loc}/checkstyle-suppressions.xml"/>
        <property name="optional" value="true"/>
    </module>

    <!-- Checks for whitespace                               -->
    <!-- See http://checkstyle.org/config_whitespace.html -->
    <!--检查文件是否包含制表符-->
    <module name="FileTabCharacter">
        <property name="eachLine" value="true"/>
    </module>
    <!--检查行长度-->
    <module name="LineLength">
        <property name="fileExtensions" value="java"/>
        <property name="max" value="120"/>
        <property name="ignorePattern" value="^package.*|^import.*|a href|href|http://|https://|ftp://"/>
    </module>

    <!--检查Java源文件并定义一些适用于检查此类文件的一些属性-->
    <module name="TreeWalker">
        <!--检查外部类型名称和文件名是否匹配，例如，类Foo必须位于名为Foo.java的文件中-->
        <module name="OuterTypeFilename"/>
        <!--检查不合规的文本-->
        <module name="IllegalTokenText">
            <property name="tokens" value="STRING_LITERAL, CHAR_LITERAL"/>
            <property name="format"
                      value="\\u00(09|0(a|A)|0(c|C)|0(d|D)|22|27|5(C|c))|\\(0(10|11|12|14|15|42|47)|134)"/>
            <property name="message"
                      value="Consider using special escape sequence instead of octal value or Unicode escaped value."/>
        </module>
        <!--限制使用Unicode转义-->
        <module name="AvoidEscapedUnicodeCharacters">
            <property name="allowEscapesForControlCharacters" value="true"/>
            <property name="allowByTailComment" value="true"/>
            <property name="allowNonPrintableEscapes" value="true"/>
        </module>
        <!--检查使用*表示的import语句-->
        <module name="AvoidStarImport"/>
        <!--检查每个顶级类，接口或枚举是否驻留在自己的源文件中-->
        <module name="OneTopLevelClass"/>
        <!--检查所选语句是否不是换行的-->
        <module name="NoLineWrap">
            <property name="tokens" value="PACKAGE_DEF, IMPORT, STATIC_IMPORT"/>
        </module>
        <!--检查空块但不验证顺序块-->
        <module name="EmptyBlock">
            <property name="option" value="TEXT"/>
            <property name="tokens"
                      value="LITERAL_TRY, LITERAL_FINALLY, LITERAL_IF, LITERAL_ELSE, LITERAL_SWITCH"/>
        </module>
        <!--检查代码块周围的大括号-->
        <module name="NeedBraces">
            <property name="tokens"
                      value="LITERAL_DO, LITERAL_ELSE, LITERAL_FOR, LITERAL_IF, LITERAL_WHILE"/>
            <!--允许没有大括号的单行语句-->
            <!--<property name="allowSingleLineStatement" value="true" />-->
        </module>
        <!--检查代码块的左花括号（'{'）的位置-->
        <module name="LeftCurly">
            <property name="tokens"
                      value="ANNOTATION_DEF, CLASS_DEF, CTOR_DEF, ENUM_CONSTANT_DEF, ENUM_DEF,
                    INTERFACE_DEF, LAMBDA, LITERAL_CASE, LITERAL_CATCH, LITERAL_DEFAULT,
                    LITERAL_DO, LITERAL_ELSE, LITERAL_FINALLY, LITERAL_FOR, LITERAL_IF,
                    LITERAL_SWITCH, LITERAL_SYNCHRONIZED, LITERAL_TRY, LITERAL_WHILE, METHOD_DEF,
                    OBJBLOCK, STATIC_INIT, RECORD_DEF, COMPACT_CTOR_DEF"/>
        </module>
        <!--检查代码块的右花括号（'}'）的位置-->
        <module name="RightCurly">
            <property name="id" value="RightCurlySame"/>
            <property name="tokens"
                      value="LITERAL_TRY, LITERAL_CATCH, LITERAL_FINALLY, LITERAL_IF, LITERAL_ELSE,
                    LITERAL_DO"/>
        </module>
        <module name="RightCurly">
            <property name="id" value="RightCurlyAlone"/>
            <property name="option" value="alone"/>
            <property name="tokens"
                      value="CLASS_DEF, METHOD_DEF, CTOR_DEF, LITERAL_FOR, LITERAL_WHILE, STATIC_INIT,
                    INSTANCE_INIT, ANNOTATION_DEF, ENUM_DEF, INTERFACE_DEF, RECORD_DEF,
                    COMPACT_CTOR_DEF"/>
        </module>
        <module name="SuppressionXpathSingleFilter">
            <!-- suppresion is required till https://github.com/checkstyle/checkstyle/issues/7541 -->
            <property name="id" value="RightCurlyAlone"/>
            <property name="query" value="//RCURLY[parent::SLIST[count(./*)=1]
                                     or preceding-sibling::*[last()][self::LCURLY]]"/>
        </module>
        <module name="WhitespaceAfter">
            <property name="tokens"
                      value="COMMA, SEMI, TYPECAST, LITERAL_IF, LITERAL_ELSE,
                    LITERAL_WHILE, LITERAL_DO, LITERAL_FOR, DO_WHILE"/>
        </module>
        <!--检查令牌是否被空格包围-->
        <module name="WhitespaceAround">
            <property name="allowEmptyConstructors" value="true"/>
            <property name="allowEmptyLambdas" value="true"/>
            <property name="allowEmptyMethods" value="true"/>
            <property name="allowEmptyTypes" value="true"/>
            <property name="allowEmptyLoops" value="true"/>
            <property name="ignoreEnhancedForColon" value="false"/>
            <property name="tokens"
                      value="ASSIGN, BAND, BAND_ASSIGN, BOR, BOR_ASSIGN, BSR, BSR_ASSIGN, BXOR,
                    BXOR_ASSIGN, COLON, DIV, DIV_ASSIGN, DO_WHILE, EQUAL, GE, GT, LAMBDA, LAND,
                    LCURLY, LE, LITERAL_CATCH, LITERAL_DO, LITERAL_ELSE, LITERAL_FINALLY,
                    LITERAL_FOR, LITERAL_IF, LITERAL_RETURN, LITERAL_SWITCH, LITERAL_SYNCHRONIZED,
                    LITERAL_TRY, LITERAL_WHILE, LOR, LT, MINUS, MINUS_ASSIGN, MOD, MOD_ASSIGN,
                    NOT_EQUAL, PLUS, PLUS_ASSIGN, QUESTION, RCURLY, SL, SLIST, SL_ASSIGN, SR,
                    SR_ASSIGN, STAR, STAR_ASSIGN, LITERAL_ASSERT, TYPE_EXTENSION_AND"/>
            <message key="ws.notFollowed"
                     value="WhitespaceAround: ''{0}'' is not followed by whitespace. Empty blocks may only be represented as '{}' when not part of a multi-block statement (4.1.3)"/>
            <message key="ws.notPreceded"
                     value="WhitespaceAround: ''{0}'' is not preceded with whitespace."/>
        </module>
        <!--检查每行只有一个语句-->
        <module name="OneStatementPerLine"/>
        <!--检查每个变量声明是否在它自己的语句中并且在它自己的行中-->
        <module name="MultipleVariableDeclarations"/>
        <!--检查数组类型定义的样式-->
        <module name="ArrayTypeStyle"/>
        <!--检查switch语句是否具有“default”子句-->
        <module name="MissingSwitchDefault"/>
        <!--检查switch语句-->
        <module name="FallThrough"/>
        <!--检查常量是否用大写定义-->
        <module name="UpperEll"/>
        <!--检查修饰符是否符合Java建议-->
        <module name="ModifierOrder"/>
        <!--检查空行分隔符-->
        <module name="EmptyLineSeparator">
            <property name="tokens"
                      value="PACKAGE_DEF, IMPORT, STATIC_IMPORT, CLASS_DEF, INTERFACE_DEF, ENUM_DEF,
                    STATIC_INIT, INSTANCE_INIT, METHOD_DEF, CTOR_DEF, VARIABLE_DEF, RECORD_DEF,
                    COMPACT_CTOR_DEF"/>
            <!--字段之间不允许空行-->
            <property name="allowNoEmptyLineBetweenFields" value="true"/>
        </module>
        <!--检查使用分隔符的换行-->
        <module name="SeparatorWrap">
            <property name="id" value="SeparatorWrapDot"/>
            <property name="tokens" value="DOT"/>
            <property name="option" value="nl"/>
        </module>
        <module name="SeparatorWrap">
            <property name="id" value="SeparatorWrapComma"/>
            <property name="tokens" value="COMMA"/>
            <property name="option" value="EOL"/>
        </module>
        <module name="SeparatorWrap">
            <!-- ELLIPSIS is EOL until https://github.com/google/styleguide/issues/258 -->
            <property name="id" value="SeparatorWrapEllipsis"/>
            <property name="tokens" value="ELLIPSIS"/>
            <property name="option" value="EOL"/>
        </module>
        <module name="SeparatorWrap">
            <!-- ARRAY_DECLARATOR is EOL until https://github.com/google/styleguide/issues/259 -->
            <property name="id" value="SeparatorWrapArrayDeclarator"/>
            <property name="tokens" value="ARRAY_DECLARATOR"/>
            <property name="option" value="EOL"/>
        </module>
        <module name="SeparatorWrap">
            <property name="id" value="SeparatorWrapMethodRef"/>
            <property name="tokens" value="METHOD_REF"/>
            <property name="option" value="nl"/>
        </module>
        <!--检查包名称是否符合format属性指定的格式-->
        <module name="PackageName">
            <property name="format" value="^[a-z]+(\.[a-z][a-z0-9]*)*$"/>
            <message key="name.invalidPattern"
                     value="Package name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查类型名称是否符合format属性指定的格式-->
        <module name="TypeName">
            <property name="tokens" value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF,
                    ANNOTATION_DEF, RECORD_DEF"/>
            <message key="name.invalidPattern"
                     value="Type name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查实例变量名称是否符合format属性指定的格式-->
        <module name="MemberName">
            <property name="format" value="^[a-z][a-z0-9][a-zA-Z0-9]*$"/>
            <message key="name.invalidPattern"
                     value="Member name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查参数名称是否符合format属性指定的格式-->
        <module name="ParameterName">
            <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
            <message key="name.invalidPattern"
                     value="Parameter name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查以验证lambda参数名称-->
        <module name="LambdaParameterName">
            <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
            <message key="name.invalidPattern"
                     value="Lambda parameter name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查catch参数名称是否符合format属性指定的格式-->
        <module name="CatchParameterName">
            <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
            <message key="name.invalidPattern"
                     value="Catch parameter name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查本地、非final变量名称是否符合format属性指定的格式-->
        <module name="LocalVariableName">
            <property name="format" value="^[a-z]([a-zA-Z0-9]*)?$"/>
            <message key="name.invalidPattern"
                     value="Local variable name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <module name="PatternVariableName">
            <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
            <message key="name.invalidPattern"
                     value="Pattern variable name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查类类型参数名称是否符合format属性指定的格式-->
        <module name="ClassTypeParameterName">
            <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
            <message key="name.invalidPattern"
                     value="Class type name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <module name="RecordTypeParameterName">
            <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
            <message key="name.invalidPattern"
                     value="Record type name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查方法类型参数名称是否符合format属性指定的格式-->
        <module name="MethodTypeParameterName">
            <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
            <message key="name.invalidPattern"
                     value="Method type name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查接口类型参数名称是否符合format属性指定的格式-->
        <module name="InterfaceTypeParameterName">
            <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
            <message key="name.invalidPattern"
                     value="Interface type name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--验证类中没有定义finalize()方法-->
        <module name="NoFinalizer"/>
        <module name="GenericWhitespace">
            <message key="ws.followed"
                     value="GenericWhitespace ''{0}'' is followed by whitespace."/>
            <message key="ws.preceded"
                     value="GenericWhitespace ''{0}'' is preceded with whitespace."/>
            <message key="ws.illegalFollow"
                     value="GenericWhitespace ''{0}'' should followed by whitespace."/>
            <message key="ws.notPreceded"
                     value="GenericWhitespace ''{0}'' is not preceded with whitespace."/>
        </module>
        <!--检查Java代码的正确缩进-->
        <module name="Indentation">
            <!--换行默认缩进字符数-->
            <property name="basicOffset" value="4" /><!--mine-->
            <!--在下一行时，大括号应缩进多远-->
            <property name="braceAdjustment" value="0" />
            <property name="caseIndent" value="4" /><!--mine-->
            <property name="throwsIndent" value="4" />
            <property name="lineWrappingIndentation" value="8" />
            <property name="arrayInitIndent" value="4" /><!--mine-->
        </module>
        <!--检查连续大写字母的长度-->
        <!--        <module name="AbbreviationAsWordInName">-->
<!--            <property name="ignoreFinal" value="false"/>-->
<!--            <property name="allowedAbbreviationLength" value="4"/>-->
<!--            <property name="tokens"-->
<!--                      value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF, ANNOTATION_DEF, ANNOTATION_FIELD_DEF,-->
<!--                    PARAMETER_DEF, VARIABLE_DEF, METHOD_DEF, PATTERN_VARIABLE_DEF, RECORD_DEF,-->
<!--                    RECORD_COMPONENT_DEF"/>-->
<!--        </module>-->
        <!--检查重载方法是否组合在一起-->
        <module name="OverloadMethodsDeclarationOrder"/>
        <!--检查变量声明与其首次使用之间的距离-->
        <module name="VariableDeclarationUsageDistance"/>
        <!--检查导入声明组是否按用户指定的顺序显示-->
        <module name="CustomImportOrder">
            <property name="customImportOrderRules"
                      value="THIRD_PARTY_PACKAGE###SPECIAL_IMPORTS###STANDARD_JAVA_PACKAGE###STATIC"/>
            <property name="specialImportsRegExp" value="^javax\."/>
            <property name="standardPackageRegExp" value="^java\."/>
            <!--是否按字母顺序导包-->
            <property name="sortImportsInGroupAlphabetically" value="true"/>
            <property name="separateLineBetweenGroups" value="false"/>
        </module>
        <!--检查方法名称和左括号之间的空格-->
        <module name="MethodParamPad">
            <property name="tokens"
                      value="CTOR_DEF, LITERAL_NEW, METHOD_CALL, METHOD_DEF,
                    SUPER_CTOR_CALL, ENUM_CONSTANT_DEF, RECORD_DEF"/>
        </module>
        <!--检查tokens指定的字符前面是否有空格-->
        <module name="NoWhitespaceBefore">
            <property name="tokens"
                      value="COMMA, SEMI, POST_INC, POST_DEC, DOT,
                    LABELED_STAT, METHOD_REF"/>
            <property name="allowLineBreaks" value="true"/>
        </module>
        <!--检查左括号后和右括号前是否需要空格-->
        <module name="ParenPad">
            <property name="tokens"
                      value="ANNOTATION, ANNOTATION_FIELD_DEF, CTOR_CALL, CTOR_DEF, DOT, ENUM_CONSTANT_DEF,
                    EXPR, LITERAL_CATCH, LITERAL_DO, LITERAL_FOR, LITERAL_IF, LITERAL_NEW,
                    LITERAL_SWITCH, LITERAL_SYNCHRONIZED, LITERAL_WHILE, METHOD_CALL,
                    METHOD_DEF, QUESTION, RESOURCE_SPECIFICATION, SUPER_CTOR_CALL, LAMBDA,
                    RECORD_DEF"/>
        </module>
        <!--检查运算符上换行的策略-->
        <!--与IDE默认行为不一致-->
        <!--        <module name="OperatorWrap">-->
<!--            <property name="option" value="NL"/>-->
<!--            <property name="tokens"-->
<!--                      value="BAND, BOR, BSR, BXOR, DIV, EQUAL, GE, GT, LAND, LE, LITERAL_INSTANCEOF, LOR,-->
<!--                    LT, MINUS, MOD, NOT_EQUAL, PLUS, QUESTION, SL, SR, STAR, METHOD_REF "/>-->
<!--        </module>-->
        <!--检查语言元素上注释的位置-->
        <module name="AnnotationLocation">
            <property name="id" value="AnnotationLocationMostCases"/>
            <property name="tokens"
                      value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF, METHOD_DEF, CTOR_DEF,
                      RECORD_DEF, COMPACT_CTOR_DEF"/>
        </module>
        <module name="AnnotationLocation">
            <property name="id" value="AnnotationLocationVariables"/>
            <property name="tokens" value="VARIABLE_DEF"/>
            <property name="allowSamelineMultipleAnnotations" value="true"/>
        </module>
        <!--检查@子句后面的描述是否为空-->
        <module name="NonEmptyAtclauseDescription"/>
        <module name="InvalidJavadocPosition"/>
        <!--检查@子句的缩进-->
        <!--        <module name="JavadocTagContinuationIndentation"/>-->
<!--        <module name="SummaryJavadoc">-->
<!--            <property name="forbiddenSummaryFragments"-->
<!--                      value="^@return the *|^This method returns |^A [{]@code [a-zA-Z0-9]+[}]( is a )"/>-->
<!--        </module>-->
        <!--检查Javadoc段落-->
        <module name="JavadocParagraph"/>
        <module name="RequireEmptyLineBeforeBlockTagGroup"/>
        <!--检查@子句的顺序-->
        <module name="AtclauseOrder">
            <property name="tagOrder" value="@param, @return, @throws, @deprecated"/>
            <property name="target"
                      value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF, METHOD_DEF, CTOR_DEF, VARIABLE_DEF"/>
        </module>
        <!--检查方法或构造函数的Javadoc-->
        <module name="JavadocMethod">
            <property name="scope" value="public"/>
            <property name="allowMissingParamTags" value="true"/>
            <property name="allowMissingReturnTag" value="true"/>
            <property name="allowedAnnotations" value="Override, Test"/>
            <property name="tokens" value="METHOD_DEF, CTOR_DEF, ANNOTATION_FIELD_DEF, COMPACT_CTOR_DEF"/>
        </module>
<!--        <module name="MissingJavadocMethod">-->
<!--            <property name="scope" value="public"/>-->
<!--            <property name="minLineCount" value="2"/>-->
<!--            <property name="allowedAnnotations" value="Override, Test"/>-->
<!--            <property name="tokens" value="METHOD_DEF, CTOR_DEF, ANNOTATION_FIELD_DEF,-->
<!--                                   COMPACT_CTOR_DEF"/>-->
<!--        </module>-->
<!--        <module name="MissingJavadocType">-->
<!--            <property name="scope" value="protected"/>-->
<!--            <property name="tokens"-->
<!--                      value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF,-->
<!--                      RECORD_DEF, ANNOTATION_DEF"/>-->
<!--            <property name="excludeScope" value="nothing"/>-->
<!--        </module>-->
        <!--检查方法名称是否符合format属性指定的格式-->
        <module name="MethodName">
            <property name="format" value="^[a-z][a-z0-9][a-zA-Z0-9_]*$"/>
            <message key="name.invalidPattern"
                     value="Method name ''{0}'' must match pattern ''{1}''."/>
        </module>
        <!--检查JavaDoc块是否可以放在一行中，并且不包含@子句-->
        <module name="SingleLineJavadoc">
            <property name="ignoreInlineTags" value="false"/>
        </module>
        <!--检查空catch块-->
        <module name="EmptyCatchBlock">
            <property name="exceptionVariableName" value="expected"/>
        </module>
        <!--控制注释和周围代码之间的缩进-->
        <!--与IDE默认行为不一致-->
        <!--        <module name="CommentsIndentation">-->
<!--            <property name="tokens" value="SINGLE_LINE_COMMENT, BLOCK_COMMENT_BEGIN"/>-->
<!--        </module>-->
        <!-- https://checkstyle.org/config_filters.html#SuppressionXpathFilter -->
        <module name="SuppressionXpathFilter">
            <property name="file" value="${org.checkstyle.google.suppressionxpathfilter.config}"
                      default="checkstyle-xpath-suppressions.xml" />
            <property name="optional" value="true"/>
        </module>
    </module>
</module>


```


