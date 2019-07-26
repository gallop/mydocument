# java 正则式基础

**Java 正则表达式**  
正则表达式定义了字符串的模式。  
正则表达式可以用来搜索、编辑或处理文本。  
正则表达式并不仅限于某一种语言，但是在每种语言中有细微的差别。

**正则表达式实例**

| 正则表达式     | 描述    |
| :------------- | :------------- |
| this is text    | 匹配字符串 "this is text"       |
|this\s+is\s+text   | 注意字符串中的 \s+。匹配单词 "this" 后面的 \s+ 可以匹配多个空格，之后匹配 is 字符串，再之后 \s+ 匹配多个空格然后再跟上 text 字符串。可以匹配这个实例：this is text  |
|^\d+(\.\d+)?   |^ 定义了以什么开始；<br> \d+ 匹配一个或多个数字；<br> ? 设置括号内的选项是可选的；<br>\. 匹配 "."；可以匹配的实例："5", "1.5" 和 "2.21"。   |

Java 正则表达式和 Perl 的是最为相似的。

java.util.regex 包主要包括以下三个类：

- Pattern 类：
pattern 对象是一个正则表达式的编译表示。Pattern 类没有公共构造方法。要创建一个 Pattern 对象，你必须首先调用其公共静态编译方法，它返回一个 Pattern 对象。该方法接受一个正则表达式作为它的第一个参数。

- Matcher 类：
Matcher 对象是对输入字符串进行解释和匹配操作的引擎。与Pattern 类一样，Matcher 也没有公共构造方法。你需要调用 Pattern 对象的 matcher 方法来获得一个 Matcher 对象。

- PatternSyntaxException：
PatternSyntaxException 是一个非强制异常类，它表示一个正则表达式模式中的语法错误。


### 捕获组
捕获组是把多个字符当一个单独单元进行处理的方法，它通过对括号内的字符分组来创建。

例如，正则表达式 (dog) 创建了单一分组，组里包含"d"，"o"，和"g"。

捕获组是通过从左至右计算其开括号来编号。例如，在表达式（（A）（B（C））），有四个这样的组：

- ((A)(B(C)))
- (A)
- (B(C))
- (C)


可以通过调用 matcher 对象的 groupCount 方法来查看表达式有多少个分组。groupCount 方法返回一个 int 值，表示matcher对象当前有多个捕获组。

**还有一个特殊的组（group(0)），它总是代表整个表达式。该组不包括在 groupCount 的返回值中。**

实例
下面的例子说明如何从一个给定的字符串中找到数字串：

```
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexMatches
{
    public static void main( String args[] ){

      // 按指定模式在字符串查找
      String line = "This order was placed for QT3000! OK?";
      String pattern = "(\\D*)(\\d+)(.*)";

      // 创建 Pattern 对象
      Pattern r = Pattern.compile(pattern);

      // 现在创建 matcher 对象
      Matcher m = r.matcher(line);
      if (m.find( )) {
         System.out.println("Found value: " + m.group(0) );
         System.out.println("Found value: " + m.group(1) );
         System.out.println("Found value: " + m.group(2) );
         System.out.println("Found value: " + m.group(3) );
      } else {
         System.out.println("NO MATCH");
      }
   }
}

```
以上实例编译运行结果如下：
```
Found value: This order was placed for QT3000! OK?
Found value: This order was placed for QT
Found value: 3000
Found value: ! OK?

```

### 正则表达式语法
在其他语言中，\\\\ 表示：我想要在正则表达式中插入一个普通的（字面上的）反斜杠，请不要给它任何特殊的意义。

在 Java 中，\\\\ 表示：我要插入一个正则表达式的反斜线，所以其后的字符具有特殊的意义。

所以，在其他的语言中（如Perl），一个反斜杠 \ 就足以具有转义的作用，而在 Java 中正则表达式中则需要有两个反斜杠才能被解析为其他语言中的转义作用。也可以简单的理解在 Java 的正则表达式中，两个 \\\\ 代表其他语言中的一个 \，这也就是为什么表示一位数字的正则表达式是 \\\\d，而表示一个普通的反斜杠是 \\\\\\\\。

| 字符    | 说明     |
| :------------- | :------------- |
| \      | 将下一字符标记为特殊字符、文本、反向引用或八进制转义符。例如，"n"匹配字符"n"。"\n"匹配换行符。序列"\\\\"匹配"\\"，"\\\\("匹配"("。       |
| ^   | 匹配输入字符串开始的位置。如果设置了 RegExp 对象的 Multiline 属性，^ 还会与"\n"或"\r"之后的位置匹配。  |
| $  | 匹配输入字符串结尾的位置。如果设置了 RegExp 对象的 Multiline 属性，$ 还会与"\n"或"\r"之前的位置匹配。  |
| *  |  零次或多次匹配前面的字符或子表达式。例如，zo* 匹配"z"和"zoo"。* 等效于 {0,}。 |
| +  | 一次或多次匹配前面的字符或子表达式。例如，"zo+"与"zo"和"zoo"匹配，但与"z"不匹配。+ 等效于 {1,}。  |
| ?  | 零次或一次匹配前面的字符或子表达式。例如，"do(es)?"匹配"do"或"does"中的"do"。? 等效于 {0,1}。  |
| {n}  | n 是非负整数。正好匹配 n 次。例如，"o{2}"与"Bob"中的"o"不匹配，但与"food"中的两个"o"匹配。  |
| {n,}  | n 是非负整数。至少匹配 n 次。例如，"o{2,}"不匹配"Bob"中的"o"，而匹配"foooood"中的所有 o。"o{1,}"等效于"o+"。"o{0,}"等效于"o*"。  |
| {n,m}  | m 和 n 是非负整数，其中 n <= m。匹配至少 n 次，至多 m 次。例如，"o{1,3}"匹配"fooooood"中的头三个 o。'o{0,1}' 等效于 'o?'。注意：您不能将空格插入逗号和数字之间。  |
| ?  | 当此字符紧随任何其他限定符（*、+、?、{n}、{n,}、{n,m}）之后时，匹配模式是"非贪心的"。"非贪心的"模式匹配搜索到的、尽可能短的字符串，而默认的"贪心的"模式匹配搜索到的、尽可能长的字符串。例如，在字符串"oooo"中，"o+?"只匹配单个"o"，而"o+"匹配所有"o"。  |
| x&#124;y  | 匹配 x 或 y。例如，'z&#124;food' 匹配"z"或"food"。'(z&#124;f)ood' 匹配"zood"或"food"。  |
| [xyz]  | 字符集。匹配包含的任一字符。例如，"[abc]"匹配"plain"中的"a"。  |
| [^xyz]  | 反向字符集。匹配未包含的任何字符。例如，"[^abc]"匹配"plain"中"p"，"l"，"i"，"n"。  |
| [a-z]  | 字符范围。匹配指定范围内的任何字符。例如，"[a-z]"匹配"a"到"z"范围内的任何小写字母。  |
| [^a-z]  | 反向范围字符。匹配不在指定的范围内的任何字符。例如，"[^a-z]"匹配任何不在"a"到"z"范围内的任何字符。  |
| \b  | 匹配一个字边界，即字与空格间的位置。例如，"er\b"匹配"never"中的"er"，但不匹配"verb"中的"er"。  |
| \B  | 非字边界匹配。"er\B"匹配"verb"中的"er"，但不匹配"never"中的"er"。  |
| \cx  | 匹配 x 指示的控制字符。例如，\cM 匹配 Control-M 或回车符。x 的值必须在 A-Z 或 a-z 之间。如果不是这样，则假定 c 就是"c"字符本身。  |
| \d  | 数字字符匹配。等效于 [0-9]。  |
| \D  | 非数字字符匹配。等效于 [^0-9]。  |
| \f  | 换页符匹配。等效于 \x0c 和 \cL。  |
| \n  | 换行符匹配。等效于 \x0a 和 \cJ。  |
| \r  | 匹配一个回车符。等效于 \x0d 和 \cM。  |
| \s  | 匹配任何空白字符，包括空格、制表符、换页符等。与 [ \f\n\r\t\v] 等效。  |
| \S  | 匹配任何非空白字符。与 [^ \f\n\r\t\v] 等效。  |
| \t  | 制表符匹配。与 \x09 和 \cI 等效。  |
| \v  | 垂直制表符匹配。与 \x0b 和 \cK 等效。  |
| \w  | 匹配任何字类字符，包括下划线。与"[A-Za-z0-9_]"等效。  |
| \W   | 与任何非单词字符匹配。与"[^A-Za-z0-9_]"等效。  |


### 大耳朵一乐 用到的正则式

> 使用场景：  
> 在小程序中，发布音乐，并发表想说的话，内容包含@微信用户昵称，需要从内容中匹配到微信用户昵称，根据昵称找到微信openId 进行微信模板消息发送。问题是，微信昵称中，有的包含emoji 图标，所以需要匹配emoji 的所有图标。

baidu中找到包含所有 emoji 的正则表达式
```
"(?:[\uD83C\uDF00-\uD83D\uDDFF]|[\uD83E\uDD00-\uD83E\uDDFF]|[\uD83D\uDE00-\uD83D\uDE4F]|[\uD83D\uDE80-\uD83D\uDEFF]|[\u2600-\u26FF]\uFE0F?|[\u2700-\u27BF]\uFE0F?|\u24C2\uFE0F?|[\uD83C\uDDE6-\uD83C\uDDFF]{1,2}|[\uD83C\uDD70\uD83C\uDD71\uD83C\uDD7E\uD83C\uDD7F\uD83C\uDD8E\uD83C\uDD91-\uD83C\uDD9A]\uFE0F?|[\u0023\u002A\u0030-\u0039]\uFE0F?\u20E3|[\u2194-\u2199\u21A9-\u21AA]\uFE0F?|[\u2B05-\u2B07\u2B1B\u2B1C\u2B50\u2B55]\uFE0F?|[\u2934\u2935]\uFE0F?|[\u3030\u303D]\uFE0F?|[\u3297\u3299]\uFE0F?|[\uD83C\uDE01\uD83C\uDE02\uD83C\uDE1A\uD83C\uDE2F\uD83C\uDE32-\uD83C\uDE3A\uD83C\uDE50\uD83C\uDE51]\uFE0F?|[\u203C\u2049]\uFE0F?|[\u25AA\u25AB\u25B6\u25C0\u25FB-\u25FE]\uFE0F?|[\u00A9\u00AE]\uFE0F?|[\u2122\u2139]\uFE0F?|\uD83C\uDC04\uFE0F?|\uD83C\uDCCF\uFE0F?|[\u231A\u231B\u2328\u23CF\u23E9-\u23F3\u23F8-\u23FA]\uFE0F?)"

```
[可以从这里获取代码 https://github.com/zly394/EmojiRegex](https://github.com/zly394/EmojiRegex)

最后的正则式匹配的代码如下：
```
private List<String> getNicknameList(String content){
        String regexStr = EmojiRegexUtil.getFullEmojiRegex();
        Pattern p = Pattern.compile("(@+)([a-z0-9A-Z\\u4e00-\\u9fa5\\s\\-_])*"+regexStr+"*([a-z0-9A-Z\\u4e00-\\u9fa5\\s\\-_])*");
        Matcher m = p.matcher(content); // 获取 matcher 对象
        List<String> nicknameList = new ArrayList<String>();
        while(m.find()){
            //System.out.println("start:"+m.start());
            //System.out.println("end:"+m.end());
            System.out.println(m.group(0));
            nicknameList.add(m.group(0).substring(1));
        }
        return nicknameList;
    }

```
